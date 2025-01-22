# 
## 适用范围：
    此内核模块基于https://github.com/strongtz/i915-sriov-dkms制作，适用于truenas scale 25.04，内核版本Linux truenas 6.12.9-production+truenas。

## 使用方法：
    1.下载i915.ko放到你的数据集中；
    2.系统→高级设置→开机/关机脚本中添加命令：
```rmmod -f i915 && insmod /mnt/Sys/Tool/modules/i915.ko max_vfs=7 enable_guc=3 && echo "1" > /sys/devices/pci0000:00/0000:00:02.0/sriov_numvfs```
    请将“/mnt/Sys/Tool/modules/i915.ko”替换为你的路径，数字“1”表示虚拟出1个sriov子设备；
    3.重启。
