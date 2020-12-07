新开坑，希望在人工智能方面有较大的进步
tensorflow-gpu版安装：
	1.显卡安装：
		# Add NVIDIA package repositories
    		wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-repo-ubuntu1804_10.0.130-1_amd64.deb
    		sudo dpkg -i cuda-repo-ubuntu1804_10.0.130-1_amd64.deb
    		sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub
    		sudo apt-get update
    		wget http://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1804/x86_64/nvidia-machine-learning-repo-ubuntu1804_1.0.0-1_amd64.deb
    		sudo apt install ./nvidia-machine-learning-repo-ubuntu1804_1.0.0-1_amd64.deb
    		sudo apt-get update

    		# Install NVIDIA driver
    		sudo apt-get install --no-install-recommends nvidia-driver-410
    		# Reboot. Check that GPUs are visible using the command: nvidia-smi
	2.安装方法（二选一）：
		(1)conda安装：
		下载anaconda，然后sh
		然后source ～/.bashrc
		输入conda install tensorflow-gpu==1.14.0

		(2)官方版安装方法：
		先安装tensorflow：
		pip install tensorflow-gpu

		# Install development and runtime libraries (~4GB)
		sudo apt-get install --no-install-recommends \
        	cuda-10-0 \
        	libcudnn7=7.6.0.64-1+cuda10.0  \
        	libcudnn7-dev=7.6.0.64-1+cuda10.0   
   		# Install TensorRT. Requires that libcudnn7 is installed above.
    		sudo apt-get update && \
            	sudo apt-get install nvinfer-runtime-trt-repo-ubuntu1804-5.0.2-ga-cuda10.0 \
            	&& sudo apt-get update \
            	&& sudo apt-get install -y --no-install-recommends libnvinfer-dev=5.0.2-1+cuda10.0
   
在训练模型过程中会出现cudnn的错误，一般为显存溢出，然后需要定义config进行改动加入到训练模型中：
config=tf.ConfigProto()
config.gpu_options.allow_growth=True
1.普通session：
session(config=congfig)
2.slim:
slim.learn.train(session_config=config)
    
