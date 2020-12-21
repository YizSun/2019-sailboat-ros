博客链接：https://blog.csdn.net/yizzsun/article/details/109267457
## 实验前 ##大（旧）船远程登录命令是：ssh sjtu@192.168.1.53 密码是：sjtu

0 配置ip

1 复制文件
```
scp ~/sailboat_ws/src/your_function.tar.gz sjtu151@192.168.1.51:/home/sjtu151/sailboat-ws/src/
```

scp ~/sailboat_ws/src/Sailboat-Ros/Class/spare_function/msg/spare_function_out.msg sjtu151@192.168.1.51:/home/sjptu151/sailboat-ws/src/Sailboat-Ros/Class/spare_function/msg/

## spare_function.cfg ## 复制动态参数文件--------------
#### 登录sjtu151端口删除文件
cd ~/sailboat-ws/src/Sailboat-Ros/Class/spare_function/cfg/
rm -f spare_function.cfg
#### 登录本机端口复制给sjtu151
scp ~/sailboat_ws/src/Sailboat-Ros/Class/spare_function/cfg/spare_function.cfg sjtu151@192.168.1.51:/home/sjtu151/sailboat-ws/src/Sailboat-Ros/Class/spare_function/cfg/

# spare_function_out.msg 复制传感器相关文件---------------
#### 登录sjtu151端口删除文件（略）
#### 登录本机端口复制给sjtu151
scp ~/sailboat_ws/src/Sailboat-Ros/Class/spare_function/msg/spare_function_out.msg sjtu151@192.168.1.51:/home/sjtu151/sailboat-ws/src/Sailboat-Ros/Class/spare_function/msg/

## example0820.py 复制主程序------------------
scp ~/sailboat_ws/src/example0822_1.py sjtu151@192.168.1.51:/home/sjtu151/sailboat-ws/src/
scp ~/sailboat_ws/src/lrx.npy sjtu151@192.168.1.51:/home/sjtu151/sailboat-ws/src/
## 复制动态参数遥控程序---------------
#### 本机
scp ~/sailboat_ws/src/example_remotecontrol.py sjtu151@192.168.1.51:/home/sjtu151/sailboat-ws/src/
#### sjtu151
cd ~/sailboat-ws
catkin clean -y
catkin build

2 远程登入(terminal1)
```
ssh sjtu151@192.168.1.51 
password sjtu
```

3 编译(terminal1) 
```
## 如果只有.py文件发生变动，则不需要编译。否则，若.cfg .msg .cpp等文件有变化时，都应重新编译
cd ~sailboat-ws/src/
解压缩
tar -zxvf your_function.tar.gz
build 文件
cd ~/sailboat-ws/
catkin clean
catkin build
```




#############################################
############## 开始进行实验 ################
#############################################
4 启动船上程序(terminal1)
```

cd ~/sailboat-ws/src/Sailboat-Ros/Sh/onboat
./start_launch_onboat_driver.sh
./start_launch_onboat.sh
nohup roslaunch pointgrey_camera_driver camera.launch &
```

5 启动岸上程序(terminal2)t
```
cd ~/sailboat_ws/src/Sailboat-Ros/Sh/onshore
./start_launch_onshore.sh
```
观察通讯和传感器数据是否正常
若出现rviz崩溃现象
```
./rviz.sh
```

6 启动autopilot算法(terminal1)
```
## 使用自制脚本启动程序
cd sailboat-ws/src/your_function/shcd
./your_function.sh
## 或直接启动py程序
cd ~/sailboat-ws/src
python example0829.py
# 照相程序
nohup python image0827.py &
## 需要手动操作自动程序时，例如遥控器无法控制帆角而程序可以控制时，启动动态参数遥控程序
cd ~/sailboat-ws/src
nohup python example_remotecontrol.py &
###### 不要忘了记录数据 ######
7 记录数据
## 记录所有数据
nohup rosbag record -a &
#20190829 有选择地记录程序:
nohup rosbag record --split --duration 60m -j -o /home/sjtu151/rosbag /wtst /mach /base/mach /arduino /sensor_kalman_msg /spare_function_out /apriltag_pub &
## 查看数据是否已位于主目录下
cd
ls
## 此时主目录下应当有对应日期的.active文件
## 检查.active文件是否正在记录，连续输入两次下列命令，观察文件大小是否变化
ll -a


nohup roslaunch pointgrey_camera_driver camera.launch &
#############################################
################# 实验结束后 ################
#############################################
实验结束后不要强行关闭主机，应正常关机
8 将数据从主板转移到本机
端口：sjtu151     查看主目录生成的文件
ls
此时可以看到类似 "2019-07-06-16-36-18.bag.active" 的文件

端口：本机     从 sjtu151 复制文件
scp sjtu151@192.168.1.51:/home/sjtu151/rosbag_2019-08-29-16-05-35_0.bag .

scp sjtu151@192.168.1.51:/home/sjtu151/images/1567056993.66_tag36h11_10.png .
注意末尾的点
#如果文件是.bag.active，则执行以下命令
rosbag reindex 2019-07-06-16-36-18.bag.active
rosbag play 2019-07-06-16-36-18.bag.active
观察是否有错误出现，如果有，则按照提示进行操作
如果没有错误，用重命名将文件的 .active 后缀去除，保留 .bag 后缀，然后运行下列命令
python bag_to_csv.py
#如果文件是.bag文件，则运行
python bag_to_csv.py


## 其它需要改的位置
## 1 wsts agjust （调整零点）

cd ~/sailboat-ws/src/Sailboat-Ros/Drivers/sensor_onboat/scripts/
vim WeatherStation_Talker.py
------line 28--------
ORIGIN_LAT = 31.0294
ORIGIN_LON = -121.439845

## 2 example0820 correct （设置目标点位）
cd ~/sailboat-ws/src
vim example0820.py
-------line 368-------
goal_list = [(-14,0),(-38,5),(-38,17)]


## 常用命令 ##-------------------------
## 编辑 arduino 程序
cd ~/sailboat-ws/src/Sailboat-Ros/Drivers/actuator_onboat/scripts
vim arduino_comm.py
## 查看节点是否存在
rosnode list
rostopic list
## 关闭某个节点
rosnode kill 回车，然后选择数字
## 单独重启某个程序
rosrun packagename filename 例如：
## 重启 arduino 程序
rosrun actuator_onboat arduino_comm.py
## 重启 WeatherStation 程序
rosrun sensor_onboat WeatherStation_Talker.py
##  
rosrun actuator_onboat base_control
rosrun sensor_onboat Ahrs_Talker.py

```


#### rostopic ####
/ahrs
/arduino
/base/mach
/joint_states
/mach
/master_discovery/changes
/master_discovery/linkstats
/odometry
/out_time
/rosout
/rosout_agg
/sensor
/sensor2
/sensor_kalman_msg
/tf
/tf_static
/wtst

#### rosnode ####
1 /Sensor_Filters
2 /Sensor_Process
3 /arduino_1255_1566461877211
4 /base_control
5 /dxl_driver
6 /example_2643_1566471244542
7 /joint_state_publisher
8 /master_discovery
9 /robot_state_publisher
10 /rosout
11 /sensor_kalman_1834_1566461918091
12 /state_publisher
13/wtst_talker_1336_1566461887143
# 启动摄像头后
14 /camera/camera_nodelet
15/camera/camera_nodelet_manager
16/camera/image_proc_debayer
17/detection
18/record_1566959875938907832

cd ~/sailboat-ws/src/Sailboat-Ros/Sh/onboat
./start_launch_onboat_driver.sh
./start_launch_onboat.sh
nohup roslaunch pointgrey_camera_driver camera.launch &
cd ~/sailboat-ws/src
nohup python image0827.py &

nohup python 0829.py &
nohup rosbag record --split --duration 60m -j -o /home/sjtu151/rosbag /wtst /mach /base/mach /arduino /sensor_kalman_msg /spare_function_out &
#启动摄像头后
cd ~/sailboat-ws/src/Sailboat-Ros/Sh/onboat
nohup roslaunch pointgrey_camera_driver camera.launch &
cd ~/sailboat-ws/src
nohup python image0827.py &
rostopic list
#选择合适的rostopic检查摄像头是否挂掉
rosnode list
#查看相关节点是否启动
cd
cd images/
ls
#查看是否拍摄到相应的二维码

## simulation

0 
```
roslaunch sailboat_launch start_tf_tree_onboat.launch
```

1 
```
roslaunch spare_function spare_function_simulation.launch
```

2 
```
cd ~/sailboat_ws/src/Sailboat-Ros/Sh/simulation
./interface.sh
```
