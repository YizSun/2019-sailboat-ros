## simulation 安装方法
在kinectic分支下载Sailboat—Ros-kinetic的压缩包，将该压缩包common的一些缺失的包复制到Sailboat-Ros的common目录下面(和船上的对应，)。然后将压缩包sh目录下的simulation目录下面的文件复制到Sailboat-Ros相对应的目录下面。
然后可以新建一个simulation工作空间，并将https://github.com/sjtu-automatic-maritime-system/Sailboat-Simulation下的文件进行download放在该工作空间的src目录下。然后在src目录下进行编译。其中发现编译错误。缺少hector_gazebo_plugins功能包。则根据（https://aur.archlinux.org/packages/ros-kinetic-hector-gazebo-plugins/）链接在终端输入命令：
git clone https://aur.archlinux.org/ros-kinetic-hector-gazebo-plugins.git
再输入一下命令：
sudo apt-get install ros-kinetic-hector-gazebo-plugins
若安装失败，需要zai系统设置里换源
然后进行catkin clean并进行catkin build即可


命令如下：

mkdir -p ~/simulation/src
cd ~/simulation/src
git clone https://github.com/sjtu-automatic-maritime-system/Sailboat-Simulation
*****************************************
如果已经换源，跳过此处进入下一个***星号分割线后：
如果没有换源：
cp /etc/apt/sources.list /etc/apt/sources.list.bak
sudo gedit /etc/apt/sources.list
复制以下内容到文档开头
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-security main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-security main restricted universe multiverse
复制到这行之前
保存并关闭
**********************************************
如果已经换源：
cd ~/sailboat_ws/src
chmod -R +x Sailboat-Ros
cd ~/simulation/src
git clone https://aur.archlinux.org/ros-kinetic-hector-gazebo-plugins.git
sudo apt-get install ros-kinetic-hector-gazebo-plugins
catkin clean
source ~/sailboat_ws/devel/setup.bash
catkin build

检查是否可以模拟：
source ~/sailboat_ws/devel/setup.bash
source ~/simulation/devel/setup.bash
roslaunch sailboat_launch start_tf_tree_onboat.launch
roslaunch spare_function spare_function_simulation.launch
cd ~/sailboat_ws/src/Sailboat-Ros/Sh/simulation
./interface.sh

