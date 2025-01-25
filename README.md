# 在truenas scale 25.04开启英特尔12-14代核显(iGPU)虚拟化sriov功能


## 适用范围：

此内核模块基于
[https://github.com/strongtz/i915-sriov-dkms](https://github.com/strongtz/i915-sriov-dkms)
制作，适用于truenas scale 25.04，英特尔12-14代核显，内核版本Linux truenas 6.12.9-production+truenas。


## 使用方法：

1.下载i915.ko放到你的数据集中；

2.系统→高级设置→开机/关机脚本中添加命令:

```
rmmod -f i915 && insmod /mnt/Sys/Tool/modules/i915.ko max_vfs=7 enable_guc=3 && echo "1" > /sys/devices/pci0000:00/0000:00:02.0/sriov_numvfs
```

请将“/mnt/Sys/Tool/modules/i915.ko”替换为你的路径，数字“1”表示虚拟出1个sriov子设备，按你自己的需求设置1-7；

3.重启生效，更新系统后仍可生效。


## 注意：

如果重启后“虚拟化(实验性)”中提示错误，点全局设定→保存一次即可恢复正常。


## 提示：

1.truenas scale 25.04 使用新的“虚拟化”(incus)功能，新的“虚拟化”分为容器(相当于LXC)和VM，如果只是想在容器上使用英特尔核显，没有必要给单独的sriov子设备，可以直接使用incus命令在主机和容器中共享显卡设备，这样即使显卡不支持sriov也可以使用，性能还更好：
```
incus config device add Debian card0 unix-char gid=44 source=/dev/dri/card0 path=/dev/dri/card0
```
```
incus config device add Debian renderD128 unix-char gid=107 source=/dev/dri/renderD128 path=/dev/dri/renderD128
```
“Debian”是你想共享的容器，gid=44中的44是你容器内video的组id，gid=107是你容器内render的组id，card0和renderD128如果你有多张显卡或者非英特尔核显不一定是这个文件名称，根据你的显卡设置。

2.incus的VM(虚拟机)和传统虚拟机功能无法直接与主机共享英特尔核显，比如我使用VM功能安装了Windows，想在Windows上使用英特尔核显，同时其他容器也需要使用英特尔核显，那么就需要sriov拆分，拆分成功后使用如下命令添加到你的incus虚拟机中：
```
incus config device add Windows iGPU pci address=0000:00:02.1
```
“Windows”是你想分给的VM(虚拟机)名称，“0000:00:02.1”为你未使用的英特尔核显sriov子设备，可以使用命令lspci查看。

3.开机会提示guc固件旧，提示下载最新guc固件，可以到这里下载最新固件解压到你的数据集中：
```
https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git/
```
truenas运行以下命令，使系统开机加载自定义固件路径：
```
midclt call system.advanced.update '{"kernel_extra_options": "firmware_class.path=/mnt/Sys/Tool/firmware"}'
```
“/mnt/Sys/Tool/firmware”替换为你的自定义固件路径。


