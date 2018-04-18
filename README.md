# Tensorflow-GPU installation on ubuntu
*Installation was done on: April 12th, 2018. Guide prepared on April 14th, 2018*

Recently, I upgraded my NVidia Graphics Card, old drivers were not compatible with the new GPU. I had a tough time to setup Tensorflow-GPU on Ubuntu. Hence, decided to make a comprehensive installation instruction guide. 

### Hardware
- Graphics Card: MSI Duke GTX 1080 <br/>
### Software
- Ubuntu 16.04.4 LTS -- Download from [here](https://www.ubuntu.com/download/desktop) <br/>
- Ubuntu Kernel - 4.13.0-38-generic (You can check this using ``` uname -r ```)
- GCC 5.4.0 20160609
- Cuda 9.0
- CudaNN 7.0
- Python 2.7.12
- Python 3.5.2
- Tensorflow 1.7.0

#### Step1: <br />
Make a bootable USB <br />
[On Windows](https://tutorials.ubuntu.com/tutorial/tutorial-create-a-usb-stick-on-windows#0) <br />
[On Ubuntu](https://tutorials.ubuntu.com/tutorial/tutorial-create-a-usb-stick-on-ubuntu#0)

*Note: If you are using windows to make a bootable USB, make sure that when you are formatting your USB Stick you select 4096 Bytes as the allocation size*

#### Step2: <br />
Boot from the USB drive, follow the instructions and install Ubuntu on your machine. 

*Note: If your system uses UEFI instead of BIOS, you need to disable Secure boot. This is mandatory for you to use third party drivers.*

#### Step3: <br />
Update all the already installed packages. 
```
sudo apt-get update
sudo apt-get upgrade
```
Make sure python, python3 are already installed. 
```
python --version
python3 --version
```
else
Install those
```
sudo apt-get install python-dev python3-dev
```

#### Step4: <br />
Install the required packages.
```
sudo apt-get install openjdk-8-jdk git python-numpy python3-numpy build-essential python-pip python3-pip python-virtualenv swig python-wheel libcurl3-dev vim
```

#### Step5: <br />
The already installed drivers for your GPU wont work with Cuda, so one has to install the proprietary drivers. For doing that blacklist the existing Noveau drivers. <br />
Create a file: 
```
sudo vi /etc/modprobe.d/nouveau
```
Add the following lines in the above file: 
```
blacklist nouveau
blacklist lbm-nouveau
options nouveau modeset=0
alias nouveau off
alias lbm-nouveau off
```
Update the initial RAM filesystem
```
echo options nouveau modeset=0 | sudo tee -a /etc/modprobe.d/nouveau-kms.conf
sudo update-initramfs -u
```
Reboot your machine:
```
sudo shutdown -r now
```
After reboot make sure the Nouveau drivers are blacklisted.
```
lsmod | grep nouveau
```
If ```nouveau``` drivers are still loaded troubleshoot the issue and then proceed with step6.

#### Step6: 

Purge NVidia drivers
```
sudo apt-get purge nvidia*  
sudo reboot
```
Reboot your machine
```
sudo shutdown -r now
```

#### Step7: 
Install Cuda. Installing Cuda will install the required NVidia Drivers. (Version: Cuda 9.0.176-1)
```
curl -O http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/cuda-repo-ubuntu1604_9.0.176-1_amd64.deb
```
Download key to allow installation
```
sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub
```
Extract the package and install
```
sudo dpkg -i ./cuda-repo-ubuntu1604_9.0.176-1_amd64.deb
sudo apt-get update
sudo apt-get install cuda-9-0  
```
Restart your machine
```
sudo shutdown -r now
```
After restart make sure NVidia drivers are installed.
```
nvidia-smi
```
This should show the GPUs on your machine, driver version (390.30 in my case), etc. If this doesnt show up troubleshoot the issue and then proceed to step8.

#### Step8:
Install CudNN. (Version: CudNN 7.0.5)
Download the required version of CudaNN from [here](https://developer.nvidia.com/compute/machine-learning/cudnn/secure/v7.0.5/prod/9.0_20171129/cudnn-9.0-linux-x64-v7). You will have to create an account to download CudNN. 
Navigate to the path where the above file was downloaded and run the following.
```
sudo tar -xzvf cudnn-9.0-linux-x64-v7.tgz
sudo cp cuda/include/cudnn.h /usr/local/cuda/include
sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
```

### Step9:
Add the following lines to your ~/.bashrc
```
export PATH=/usr/local/cuda-9.0/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda-9.0/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```
Reload ~/.bashrc
```
source ~/.bashrc
```

#### Step10:
Install tensorflow-gpu
- For python 2.7
```
pip install tensorflow-gpu
```
- For python 3.5
```
pip3 install tensorflow-gpu
```

#### Step11:
Test tensorflow installation
Open python in a terminal
```
python  #for python 2.7
python3 #for python 3.5
```
Run the following commands.
```
import tensorflow as tf
sess = tf.Session()
```
If the installation was successful, you should see many lines with gpu and gpu model in them.
