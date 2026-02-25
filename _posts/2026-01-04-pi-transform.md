---
title: transform in pi
date: 2025-12-18 16:24
category: 
author: 
tags: []
summary: 
image:
    path: 
math: True
---

关于pi中的input/output transform

有几点比较重要的点:

1. **compute_norm_stat和train中都使用了config，因此不存在归一化空间与推理时使用的空间不一致的情况**

2. 训练阶段只使用了input transform, 推理阶段使用了input & output transform

3. 

~~~
    # create_trained_policy
    # 注意: 这只是训练阶段的结构
    return _policy.Policy(
        model,
        transforms=[
            *repack_transforms.inputs,
            transforms.InjectDefaultPrompt(default_prompt),
            *data_config.data_transforms.inputs,
            transforms.Normalize(norm_stats, use_quantiles=data_config.use_quantile_norm),
            *data_config.model_transforms.inputs,
        ],
        output_transforms=[
            *data_config.model_transforms.outputs,
            transforms.Unnormalize(norm_stats, use_quantiles=data_config.use_quantile_norm),
            *data_config.data_transforms.outputs,
            *repack_transforms.outputs,
        ],
        sample_kwargs=sample_kwargs,
        metadata=train_config.policy_metadata,
        is_pytorch=is_pytorch,
        pytorch_device=pytorch_device if is_pytorch else None,
    )
~~~

~~~
data_config: 
DataConfig(
    repo_id='/s3data/public/VLA/datasets/custom/pick_cillion_v3', 
    asset_id='/s3data/public/VLA/datasets/custom/pick_cillion_v3', 
norm_stats={
       'state': NormStats(
           mean=array([0. , 0. , 0. , 0. , 0. , 0. , 0.41200912]), 
           std=array([0. , 0. , 0. , 0. , 0. , 0. , 0.49219671]), 
           q01=array([ 0.e+00,  0.e+00,  0.e+00,  0.e+00,  0.e+00,  0.e+00, -1.e-10]), 
           q99=array([0.    , 0.    , 0.    , 0.    , 0.    , 0.    , 0.9998])), 
       'actions': NormStats(
           mean=array([ 0.01362549, -0.00629715,  0.00678807, -0.08124758, -0.0018835 , 0.00211264,  0.41229829]), 
           std=array([0.04590553, 0.03231804, 0.02966013, 0.24646623, 0.08277468, 0.04696944, 0.49224833]), 
           q01=array([-0.10785829, -0.14041051, -0.09575055, -1.05917914, -0.2988256 , -0.14042391,  0. ]), 
           q99=array([0.14642567, 0.06528913, 0.10951538, 0.33979979, 0.21542331, 0.14434674, 0.9998    ]))
       }, 
       repack_transforms=Group(
           inputs=[RepackTransform(structure={'wrist_image_left': 'wrist_image_left', 'wrist_image_right': 'wrist_image_right', 'state': 'state', 'actions': 'actions', 'prompt': 'prompt'})], 
           outputs=()), 
       data_transforms=Group(
           inputs=(PikaInputs(model_type=<ModelType.PI05: 'pi05'>), RelativePoseTransform(pose_dims=(0, 6))), 
           outputs=(AbsolutePoseTransform(pose_dims=(0, 6)), PikaOutputs())), 
       model_transforms=Group(inputs=[InjectDefaultPrompt(prompt=None), ResizeImages(height=224, width=224), 
       TokenizePrompt(tokenizer=<openpi.models.tokenizer.PaligemmaTokenizer object at 0x7fa1f8309390>, discrete_state_input=False), 
       PadStatesAndActions(model_action_dim=32)], outputs=()), use_quantile_norm=True, action_sequence_keys=('actions',), prompt_from_task=True, rlds_data_dir=None, action_space=None, filter_dict_path=None) 
       (60276:data_loader.py:243)
~~~


## loss

~~~
    def compute_loss(
        self, rng: at.KeyArrayLike, observation: _model.Observation, actions: _model.Actions, *, train: bool = False
    ) -> at.Float[at.Array, "*b ah"]:
        preprocess_rng, noise_rng, time_rng = jax.random.split(rng, 3)
        observation = _model.preprocess_observation(preprocess_rng, observation, train=train)

        batch_shape = actions.shape[:-2]
        noise = jax.random.normal(noise_rng, actions.shape)
        time = jax.random.beta(time_rng, 1.5, 1, batch_shape) * 0.999 + 0.001
        time_expanded = time[..., None, None]
        x_t = time_expanded * noise + (1 - time_expanded) * actions
        u_t = noise - actions

        # one big forward pass of prefix + suffix at once
        prefix_tokens, prefix_mask, prefix_ar_mask = self.embed_prefix(observation)
        suffix_tokens, suffix_mask, suffix_ar_mask, adarms_cond = self.embed_suffix(observation, x_t, time)
        input_mask = jnp.concatenate([prefix_mask, suffix_mask], axis=1)
        ar_mask = jnp.concatenate([prefix_ar_mask, suffix_ar_mask], axis=0)
        attn_mask = make_attn_mask(input_mask, ar_mask)
        positions = jnp.cumsum(input_mask, axis=1) - 1
        (prefix_out, suffix_out), _ = self.PaliGemma.llm(
            [prefix_tokens, suffix_tokens], mask=attn_mask, positions=positions, adarms_cond=[None, adarms_cond]
        )
        v_t = self.action_out_proj(suffix_out[:, -self.action_horizon :])

        return jnp.mean(jnp.square(v_t - u_t), axis=-1)
~~~

首先从均值0方差1的高斯分布中采样噪声(noise_rng作为seed)，采样一个和actions.shape一样大的噪声，确保能够附加到作为监督信息的actions上

~~~
noise = jax.random.normal(noise_rng, actions.shape)
~~~

