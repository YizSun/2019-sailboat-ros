#打开bshrc文件
gedit ~/.bashrc
#将最后的注释取消
__conda_setup="$('/home/liyunjia/anaconda3/bin/conda' 'shell.bash' 'hook' 2> /dev/null)"
if [ $? -eq 0 ]; then
    eval "$__conda_setup"
else
    if [ -f "/home/liyunjia/anaconda3/etc/profile.d/conda.sh" ]; then
        . "/home/liyunjia/anaconda3/etc/profile.d/conda.sh"
    else
export PATH="/home/liyunjia/anaconda3/bin:$PATH"
    fi
fi
unset __conda_setup
#将ROS的source进行注释掉
#重新打开终端，输入命令并选择合适python版本
sudo update-alternatives  --config python
#输入以下命令查看现有的python版本
python
#输入该命令打开juptyer notebook



#切换到python2.7
workon env27

