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
## adb屏幕录制
***
> screenrecord是一个shell命令，支持**Android4.4**(API level 19)以上，录制的视频格式为**mp4** ,存放到手机sd卡里，默认录制时间为**180s**  
  
````
adb shell screenrecord --size 1280*720 --bit-rate 6000000 --time-limit 30 /sdcard/demo.mp4  
 --size 指定视频分辨率；  
 --bit-rate 指定视频比特率，默认为4M，该值越小，保存的视频文件越小；  
 --time-limit 指定录制时长，若设定大于180，命令不会被执行；   
````

## adb截屏
***
adb shell screencap -p | sed 's/\r$//' > D:/screen.png
 > 截图并将图片导出到D盘，保存文件名screen，格式可以指定为jpg或png
## 常用简单命令
***
* adb push D:/test.txt /sdcard/  PC端文件导入设备/sdcard路径下
* adb pull /sdcard/test.txt D:/  将设备/sdcard/目录下文件导出到电脑D盘
* adb install demo.apk 在设备上安装demo.apk
* adb logcat 查看系统logcat日志
* 当PC同时连接多个设备，adb -s udid <command> ， udid为手机唯一标识，可通过设置中关于手机查看
