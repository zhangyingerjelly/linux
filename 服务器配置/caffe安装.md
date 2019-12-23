# 1.  opencv
一定要先安装opencv，由于CUDA10.0,因此选择opencv3.4.5(必须大于3.2) <br>
https://blog.csdn.net/hhhuua/article/details/80436160 <br>
先安装依赖库，注意编译时python路径<br>
cmake
make -j8
make install <br>
pkg-config --modversion opencv 查看版本信息 (一定要看是不是自己装的那个，否则若预装过.so文件，会引发caffe对应错误)

# 2. caffe-gpu
存在anaconda3,但使用的是原来环境中python3.6 。因为不知道Makefile中怎么改<br>
## 1. cp Makefile.config.example Makefile.config
1. USE_CUDNN := 1 <br>
2. OPENCV_VERSION := 3  <br>
3. WITH_PYTHON_LAYER := 1 （不确定是不是一定要的）；且rrule.py文件有个错误，该文件用python2写的，要么手动修改，要么将某个模块grade <br>
4. 修改python路径: <br>
INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include /usr/include/hdf5/serial <br>
LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib /usr/lib/x86_64-linux-gnu /usr/lib/x86_64-linux-gnu/hdf5/serial <br>
5. cuda_arch:去掉前两行 20 和 21信息 <br>
6. 类似下面，改成系统的python3,,注意python3/dist-packages <br>
  PYTHON_INCLUDE := $(ANACONDA_HOME)/include \ <br>
  $(ANACONDA_HOME)/include/python3.6m \ <br>
  $(ANACONDA_HOME)/lib/python3.6/site-packages/numpy/core/include \ <br>
                  /usr/include/python2.7  <br>
7. PYTHON_LIBRARIES := boost_python3 python3.6m <br>
同时，建立软链接，在另一个终端输入 <br>
sudo ln -s /usr/lib/x86_64-linux-gnu/libboost_python-py35.so.1.58.0 /usr/local/lib/libboost_python3.so <br>
## 2.配置Makefile
1. NVCCFLAGS += -D_FORCE_INLINES -ccbin=$(CXX) -Xcompiler -fPIC $(COMMON_FLAGS) <br>
2. LIBRARIES += glog gflags protobuf boost_system boost_filesystem m hdf5_serial_hl hdf5_serial  
3. with_pythonlayer?=改成3.6

## 3.编译
1. make clean <br>
2. make all -j8 <br>
出现libglad.so。20  sqlite的报错（进行以下操作，但不知道是哪个原因）：<br>
  1. anaconda 环境冲突，所以注释掉了~/.bashrc中的anaconda环境 <br>
  2.apt remove sqlite3 <br>
  3.安装opencv3.4.5 <br>
3. make runtest -j8 <br>
4. make pycaffe
