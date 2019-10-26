# Enabling nvidia gpu in docker on manjaro
To have gpu support in docker tensorflow we need to add gpu support to docker
## Steps
### Docker
`sudo pacman -S docker`

`sudo systemctl start docker`

`sudo systemctl enable docker` start docker after system reboot

### nvidia-docker
[nvidia-docker](https://aur.archlinux.org/packages/nvidia-docker/) can not be install directly using pacman. It have to be build from sources.
#### Install yay
One of arch packages tools
`sudo pacman -S yay`

`yay -S nvidia-docker` when asked for dependencies (nvidia-container...) select "bin" packages

This should build and install nvidia-docker.

`sudo systemctl restart docker` should activated nvidia runtime in docker
### Test
`docker run --runtime=nvidia --rm nvidia/cuda nvidia-smi`

Should return something like this:
```
Fri Jul 26 21:10:10 2019       
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 430.26       Driver Version: 430.26       CUDA Version: 10.2     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  GeForce GTX 1070    Off  | 00000000:01:00.0  On |                  N/A |
| N/A   47C    P8     8W /  N/A |    237MiB /  8111MiB |      9%      Default |
+-------------------------------+----------------------+----------------------+
                                                                               
+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
+-----------------------------------------------------------------------------+
```
# Jupyter notebook with nvidia GPU
## Run tensorflow image
Tensorflow project provides ready to use images with [TF and jupyter](https://www.tensorflow.org/install/docker).

Basic command to run notebook is:
```
docker run --runtime=nvidia -it -p 8888:8888 --name deep_learning_keras tensorflow/tensorflow:nightly-py3-jupyter
```
TF image contains floder `/tf` with `tensorflow-tutorials` notebooks folder. To add your own notebook which is outside container use docker's '-v' param.
```
docker run --runtime=nvidia -it -p 8888:8888 -v /home/lucjan/Documents/notebooks:/tf/notebooks --name deep_learning_keras tensorflow/tensorflow:nightly-py3-jupyter
```
## Install additional dependencies
<s>TF image don't contain keras. To install it connect to container `docker exec -it deep_learning_keras bash` and install keras `pip install keras`.</s>

TF2 image have keras installed already.

Connect to container `docker exec -it deep_learning_keras bash`

To be able to display keras model structure install:
```
apt update
apt upgrade
apt install graphviz
pip install  pydot pydotplus graphviz
```
## Open notebook
Link to notebook with session will be in logs.
