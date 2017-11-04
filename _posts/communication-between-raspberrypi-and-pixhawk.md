---

title: pixhawk与raspberryPI连接教程
date: 2017-10-20 11:47:14
tags: rpi3b
categories: Linux

---

# pixhawk与raspberryPI连接教程

官方文档过于陈旧，一些内容已经过时，写这篇文档希望后来的人少走一些弯路


## STEP 1：接线

![../_images/RaspberryPi_Pixhawk_wiring1.jpg](http://ardupilot.org/dev/_images/RaspberryPi_Pixhawk_wiring1.jpg)

按上图接线 （**注意：由于飞控和树莓派的接口型号不同，连接用的线需要专门定制**）

<!--more-->

## STEP 2：更改飞控参数 

·       [SERIAL2_PROTOCOL](http://ardupilot.org/copter/docs/parameters.html#serial2-protocol) = 1 指明飞控上使用的是TELEM1口

·       [SERIAL2_BAUD](http://ardupilot.org/copter/docs/parameters.html#serial2-baud) = 921 波特率为921600

·       [LOG_BACKEND_TYPE](http://ardupilot.org/copter/docs/parameters.html#log-backend-type) = 3 

## STEP 3：通过SSH命令连接到树莓派

如果使用以太网线将电脑和树莓派连接的话，键入下面的命令

```shell
ssh  pi@raspberrypi.local
```


如果没有网线而树莓派已经接入网络的话

```shell
ssh  pi@raspberry-pi's-ip-address
```

获取树莓派IP的方法有很多，如修改`/etc/wpa_supplicant/wpa_supplicant.cfg`配置wifi连接，后在路由器的控制面板中查看

## STEP 4：配置树莓派

在ssh界面键入一下命令

```shell
sudo apt-get update    #Update the list of packages in the software center
sudo apt-get install screen python-wxgtk2.8 python-matplotlib python-opencv python-pip python-numpy python-dev libxml2-dev libxslt-dev
sudo pip install future
sudo pip install pymavlink
sudo pip install mavproxy
```

之后需要禁掉树莓派系统使用串口的权限

```shell
sudo raspi-config
```

**在出现的蓝色界面里进入带“Interface”那个分支，然后再选择带“serial”的选项， 第一个“no”，第二个“yes”**（这里官网的文档过于陈旧，不可用）

## STEP 5：测试连接

```shell
sudo -s
# 相较官方文档，更改了串口号和波特率
mavproxy.py --master=/dev/serial0 --baudrate 921600 --aircraft MyCopter 
```

如果出现以下界面，说明连接成功

![../_images/RaspberryPi_ArmTestThroughPutty.png](http://ardupilot.org/dev/_images/RaspberryPi_ArmTestThroughPutty.png)

## STEP 6：使MavProxy开机启动

修改`/etc/rc.local`文件，在`exit0`前添加

```shell
(
date
echo $PATH
PATH=$PATH:/bin:/sbin:/usr/bin:/usr/local/bin
export PATH
cd /home/pi
screen -d -m -s /bin/bash mavproxy.py --master=/dev/serial0 --baudrate 921600 --aircraft MyCopter
) > /tmp/rc.log 2>&1
exit 0
```

## STEP7：安装DroneKit

```shell
sudo apt-get install python-pip python-dev python-numpy python-opencv python-serial python-pyparsing python-wxgtk2.8 libxml2-dev libxslt-dev
sudo pip install droneapi
echo "module load droneapi.module.api" >> ~/.mavinit.scr
```

## STEP8：连接地面站

到这基本上就算大功告成了，在raspberryPi上

```shell
mavproxy.py --master=/dev/serial0 --baudrate 921600 --out your-ip-here:14550 --aircraft MyCopter
```

之后在地面站如下图所示连接

![../_images/RaspberryPi_MissionPlanner.jpg](http://ardupilot.org/dev/_images/RaspberryPi_MissionPlanner.jpg)

