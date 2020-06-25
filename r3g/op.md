# 一、刷机前的准备工作  
## 刷入开发版ROM  
  
1.在[小米路由器官网](http://www.miwifi.com/miwifi_download.html)上下载R3G 开发版固件  
  
2.在小米路由器上选择系统升级，然后选择ROM进行升级，等重启完成后即可。  
  
## 开启路由器ssh登录  
1.先让路由器绑定账号，打开小米WIFI app，然后需要手机和路由器一个网络，登录后即可绑定  
  
2.在[网站](http://www.miwifi.com/miwifi_open.html)中找到开启SSH工具，登录小米账号，会显示root密码和ssh文件，注意这个文件每个路由器都不一样。  
  
3.请将下载的工具包bin文件复制到U盘（FAT/FAT32格式）的根目录下，保证文件名为miwifi_ssh.bin。  
  
4.断开小米路由器的电源，将U盘插入USB接口。  
  
5.按住reset按钮之后重新接入电源，指示灯变为黄色闪烁状态即可松开reset键。  
  
6.等蓝灯亮起即可刷机完成。  
  
7.ssh root@192.168.1.1即可登录到路由器上  
  
# 二、刷入breed
`这一步是防止路由被刷坏`  
  
1.在[breed发布地址](https://breed.hackpascal.net/)下载breed-mt7621-xiaomi-r3g.bin  
  
2.然后将文件重命名为breed.bin后，上传到小米路由器，可以使用U盘或者使用SCP上传，上传到/tmp下  
  
3.进入到小米路由开始写入
> breed mtd -r write /tmp/breed.bin Bootloader  
  
4.刷入后，机器会重新启动，按住reset键开机，等到路由指示灯闪烁时，松开reset键，然后浏览器中输入192.168.1.1即可进入breed后台。  
  
5.备份后重启路由回到小米固件的，然后ssh登陆准备刷入openwrt  
  
# 三、刷入openwrt  
1.下载对应的文件，并将*kernel1.bin *rootfs0.bin上传到路由器。  
`由于固件较大，建议放入U盘后刷入，否则可能内存溢出导致失败`  
  
### 2-1.确定分区格式  
1.进入ssh，输入下列指令  
> cat /proc/mtd  
  
确定name下有kernel0 kernel1 rootfs0  
  
### 2-2-1.由于我刷入了breed，需要执行  
  ```  
#先cd到目录
$ mtd write openwrt-ramips-mt7621-mir3g-squashfs-kernel1.bin kernel0
$ mtd write openwrt-ramips-mt7621-mir3g-squashfs-kernel1.bin kernel1
$ mtd write openwrt-ramips-mt7621-mir3g-squashfs-rootfs0.bin rootfs0
$ reboot
```  
  
### 2-2-2.如果没有刷入breed，则需要执行  
  ```
#先cd到目录
$ mtd write openwrt-ramips-mt7621-mir3g-squashfs-kernel1.bin kernel1
$ mtd write openwrt-ramips-mt7621-mir3g-squashfs-rootfs0.bin rootfs0
$ nvram set flag_try_sys1_failed=1
$ nvram commit
$ reboot
```  
`刷入固件后请勿关闭路由器，要接着执行如下步骤，否则ssh会失效，如果ssh失效，请试着重置路由器。`  
  
3.浏览器登陆192.168.1.1，在界面选择系统，备份/升级，刷写新的固件，上传openwrt-ramips-mt7621-mir3g-squashfs-sysupgrade.tar，升级版本，即可完成。  
  
5.如果重置路由器，等重启完成后web界面是被重置没有了，执行ssh root@192.168.1.1，设置密码重新安装luci。  
  
`https://blog.csdn.net/z619193774/article/details/81507917`