#adb
***
 整理一些adb命令
## abd wifi connect
***
1. adb devices 查看所有连接设备的udid
1. adb tcpip 5555 设定tcp端口
1. adb connect device_ip:5555 （ip可在设备的wifi里看到）

**设备拒绝连接情况** 

1. 设备安装终端模拟器软件，通过kingroot等软件获取root权限，在模拟器中输入如下命令  
1. su
1. setprop service.adb.tcp.port 5555
1. stop adbd
1. start adbd
1. PC端执行adb connect device_ip:5555

## adb chmod
***
* 文件系统权限允许时：  
  1. adb shell
  1. su
  1. chmod 777 file_path
    
* 不允许时,对文件进行重挂载  
  1. adb shell
  1. su
  1. mount
  1. 找出需重写目录的挂载节点，复制其名称，如/dev
  1. mount -o rw,remount /dev path，path对应子目录均可读写