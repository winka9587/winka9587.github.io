---
title: ho3d dataset visualization 
date: 2025-06-20 18:05
category: 
author: 
tags: []
summary: 
image:
    path: /assets/img/2025-06-20-18-05-48.png
---

~~~
export MANO_PATH=/home/cxx/workspace/HO3R/ho3d/mano/mano_v1_2
export YCB_PATH=/home/cxx/workspace/HO3R/ho3d/ycb_models/models
export DB_PATH=/home/cxx/dataset/HO3D_v3
~~~

~~~
python setup_mano.py ${MANO_PATH}
~~~

成功运行所需的环境配置如下: 

~~~
python 2.7
Package                            Version            
---------------------------------- -------------------
attrs                              21.4.0             
backports-abc                      0.5                
backports.shutil-get-terminal-size 1.0.0              
bleach                             3.3.1              
certifi                            2020.6.20          
chumpy                             0.70               
configparser                       4.0.2              
contextlib2                        0.6.0.post1        
Cython                             3.0.12             
decorator                          5.1.0              
defusedxml                         0.7.1              
entrypoints                        0.3                
enum34                             1.1.6              
functools32                        3.2.3.post2        
futures                            3.3.0              
importlib-metadata                 1.3.0              
ipaddress                          1.0.23             
ipykernel                          4.10.0             
ipython                            5.8.0              
ipython-genutils                   0.2.0              
ipywidgets                         7.6.0              
Jinja2                             2.11.3             
jsonschema                         3.2.0              
jupyter-client                     5.3.4              
jupyter-core                       4.6.1              
MarkupSafe                         1.1.1              
mistune                            0.8.4              
more-itertools                     5.0.0              
nbconvert                          5.6.1              
nbformat                           4.4.0              
notebook                           5.7.10             
numpy                              1.16.6             
open3d                             0.8.0.0            
packaging                          20.9               
pandocfilters                      1.5.0              
pathlib2                           2.3.5              
pexpect                            4.8.0              
pickleshare                        0.7.5              
pip                                19.3.1             
prometheus-client                  0.11.0             
prompt-toolkit                     1.0.15             
ptyprocess                         0.7.0              
Pygments                           2.5.2              
pyparsing                          2.4.7              
pyrsistent                         0.15.6             
python-dateutil                    2.8.2              
pyzmq                              18.1.0             
scandir                            1.10.0             
scipy                              1.2.1              
Send2Trash                         1.8.0              
setuptools                         44.0.0.post20200106
simplegeneric                      0.8.1              
singledispatch                     3.7.0              
six                                1.16.0             
terminado                          0.8.3              
testpath                           0.4.4              
tornado                            5.1.1              
traitlets                          4.3.3              
wcwidth                            0.2.5              
webencodings                       0.5.1              
wheel                              0.37.1             
widgetsnbextension                 3.5.1              
zipp                               0.6.0 
~~~

~~~
pip install decorator==4.4.2
pip install scikit-image==0.14.5
~~~

运行
~~~
python vis_HO3D.py ${DB_PATH} ${YCB_PATH}
~~~

![](/assets/img/2025-06-20-18-05-58.png)

![](/assets/img/2025-06-20-18-05-48.png)

---

> libGL error: MESA-LOADER: failed to open swrast: /usr/lib/dri/swrast_dri.so: cannot open shared object file: No such file or directory (search paths /usr/lib/x86_64-linux-gnu/dri:\$${ORIGIN}/dri:/usr/lib/dri, suffix _dri)
> libGL error: failed to load driver: swrast

~~~
sudo ln -s /lib/x86_64-linux-gnu/dri/swrast_dri.so /usr/lib/dri/swrast_dri.so
~~~

---
> libGL error: MESA-LOADER: failed to open swrast: /home/cxx/miniconda3/envs/ho3d_dataset/bin/../lib/libstdc++.so.6: version `GLIBCXX_3.4.30' not found (required by /lib/x86_64-linux-gnu/libLLVM-15.so.1) (search paths /usr/lib/x86_64-linux-gnu/dri:\$${ORIGIN}/dri:/usr/lib/dri, suffix _dri)
> libGL error: failed to load driver: swrast
~~~
sudo find /usr /lib /opt /home -name "libstdc++.so.6" 2>/dev/null
~~~

~~~
export LD_PRELOAD=/usr/lib/x86_64-linux-gnu/libstdc++.so.6
~~~

