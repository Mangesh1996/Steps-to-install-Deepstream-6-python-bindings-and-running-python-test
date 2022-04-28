<h1>Steps to install Deepstream-6, python bindings and running python test app3</h1><br>
<h2>***Needed nvidia driver 470 (if not present it will get installed during cuda installation)</h2>
    
<h2>Step 1: Install Cuda 11.4</h2><br>

    $ sudo apt-get install gcc
    $ sudo apt-get install make
    $ wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
    $ sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600
    $ wget https://developer.download.nvidia.com/compute/cuda/11.4.0/local_installers/cuda-repo-ubuntu1804-11-4-local_11.4.0-470.42.01-1_amd64.deb
    $ sudo dpkg -i cuda-repo-ubuntu1804-11-4-local_11.4.0-470.42.01-1_amd64.deb
    $ sudo apt-key add /var/cuda-repo-ubuntu1804-11-4-local/7fa2af80.pub
    $ sudo apt-get update
    $ sudo apt-get -y install cuda
    $ sudo nano ~/.bashrc
    
    #paste these 2 lines at the end of bashrc file
    export PATH=/usr/local/cuda-11.4/bin:$PATH
    export LD_LIBRARY_PATH=/usr/local/cuda-11.4/lib64:$LD_LIBRARY_PATH
    $ source ~/.bashrc
    
<h2>Step 2: Install cudnn 8.2.2.26</h2><br>
Download cudnn 8.2.2.26 tar file<br>

    $ tar -xzvf cudnn-11.4-linux-x64-v8.2.2.26.tgz
    $ sudo cp cuda/include/cudnn*.h /usr/local/cuda/include
    $ sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
    $ sudo chmod a+r /usr/local/cuda/include/cudnn*.h /usr/local/cuda/lib64/libcudnn*
    
<h2>Step 3: Installing tensorrt 8.2.1.8</h2><br>
Download tensorrt 8.2.1.8 deb file<br>

    $ os="ubuntu1804"
    $ tag="cuda11.4-trt8.2.1.8-ga-20211117"
    $ sudo dpkg -i nv-tensorrt-repo-${os}-${tag}_1-1_amd64.deb
    $ sudo apt-key add /var/nv-tensorrt-repo-ubuntu1804-cuda11.4-trt8.2.1.8-ga-20211117/7fa2af80.pub
    $ sudo apt-get update
    $ sudo apt-get install tensorrt -y
    $ sudo apt-get install python3-libnvinfer-dev -y
    $ sudo apt-get install uff-converter-tf -y
    $ dpkg -l | grep TensorRT
   
[Optional] Remove symbloic links<br>

    $ sudo ln -sf /usr/local/cuda-11.4/targets/x86_64-linux/lib/libcudnn_ops_infer.so.8.2.2 /usr/local/cuda-11.4/targets/x86_64-linux/lib/libcudnn_ops_infer.so.8
    $ sudo ln -sf /usr/local/cuda-11.4/targets/x86_64-linux/lib/libcudnn_adv_train.so.8.2.2 /usr/local/cuda-11.4/targets/x86_64-linux/lib/libcudnn_adv_train.so.8
    $ sudo ln -sf /usr/local/cuda-11.4/targets/x86_64-linux/lib/libcudnn_adv_infer.so.8.2.2 /usr/local/cuda-11.4/targets/x86_64-linux/lib/libcudnn_adv_infer.so.8
    $ sudo ln -sf /usr/local/cuda-11.4/targets/x86_64-linux/lib/libcudnn_cnn_infer.so.8.2.2 /usr/local/cuda-11.4/targets/x86_64-linux/lib/libcudnn_cnn_infer.so.8
    $ sudo ln -sf /usr/local/cuda-11.4/targets/x86_64-linux/lib/libcudnn.so.8.2.2 /usr/local/cuda-11.4/targets/x86_64-linux/lib/libcudnn.so.8
    $ sudo ln -sf /usr/local/cuda-11.4/targets/x86_64-linux/lib/libcudnn_ops_train.so.8.2.2 /usr/local/cuda-11.4/targets/x86_64-linux/lib/libcudnn_ops_train.so.8
    $ sudo ln -sf /usr/local/cuda-11.4/targets/x86_64-linux/lib/libcudnn_cnn_train.so.8.2.2 /usr/local/cuda-11.4/targets/x86_64-linux/lib/libcudnn_cnn_train.so.8

<h2>Step 4: Installing Deepstream 6</h2><br>
Uninstall any previous deepstream version<br>
Download the deepstream 6 tar file<br>

    $ sudo apt-get install libtool m4 automake
    $ sudo apt install \
    libssl1.0.0 \
    libgstreamer1.0-0 \
    gstreamer1.0-tools \
    gstreamer1.0-plugins-good \
    gstreamer1.0-plugins-bad \
    gstreamer1.0-plugins-ugly \
    gstreamer1.0-libav \
    libgstrtspserver-1.0-0 \
    libjansson4 \
    libgstreamer1.0-dev
    $ sudo apt-get install libgstreamer-plugins-base1.0-dev -y
    $ sudo tar -xvf deepstream_sdk_v6.0.0_x86_64.tbz2 -C /
    $ cd /opt/nvidia/deepstream/deepstream-6.0/
    $ sudo ./install.sh
    $ sudo ldconfig
    
<h2>Step 5:Installing python binding</h2><br>

    $ sudo apt-get install python-gi-dev
    $ sudo apt-get install python3-dev libpython3-dev
    $ export GST_LIBS="-lgstreamer-1.0 -lgobject-2.0 -lglib-2.0"
    $ export GST_CFLAGS="-pthread -I/usr/include/gstreamer-1.0 -I/usr/include/glib-2.0 -I/usr/lib/x86_64-linux-gnu/glib-2.0/include"
    $ git clone https://github.com/GStreamer/gst-python.git
    $ cd gst-python
    $ git checkout 1a8f48a
    $ ./autogen.sh PYTHON=python3
    $ ./configure PYTHON=python3
    $ make
    $ sudo make install
    $ cd sources
    $ sudo git clone https://github.com/NVIDIA-AI-IOT/deepstream_python_apps
   
Making pyds
    
    $ cd /opt/nvidia/deepstream/deepstream/sources/apps/
    $ cd deepstream_python_apps/
    $ git submodule update --init
    $ sudo apt-get install --reinstall ca-certificates
    $ cd 3rdparty/gst-python/
    $ ./autogen.sh PYTHON=python3
    $ make
    $ sudo make install
    $ cd deepstream_python_apps/bindings/
    $ mkdir build
    $ cd build
    $ cmake ..
    $ sudo make
    $ pip3 install ./pyds-1.1.0-py3-none-linux_x86_64.whl
    
<h2>Step 6: Running testapp</h2><br>

    $ cd /opt/nvidia/deepstream/deepstream-6.0/sources/deepstream_python_apps/apps/deepstream-test3
    $ python3 deepstream_test_3.py file:///opt/nvidia/deepstream/deepstream-6.0/samples/streams/sample_1080p_h264.mp4
    
<h1>File name</h1>
cuda-repo-ubuntu1804-11-4-local_11.4.0-470.42.01-1_amd64.deb<br>
cudnn-11.4-linux-x64-v8.2.2.26.tgz<br>
nv-tensorrt-repo-ubuntu1804-cuda11.4-trt8.2.1.8-ga-20211117_1-1_amd64.deb<br>
deepstream_sdk_v6.0.0_x86_64.tbz2<br>
