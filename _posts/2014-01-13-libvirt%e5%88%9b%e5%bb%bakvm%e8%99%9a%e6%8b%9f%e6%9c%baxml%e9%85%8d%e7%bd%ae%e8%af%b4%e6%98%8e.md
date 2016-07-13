---
id: 27
title: libvirt创建kvm虚拟机配置说明
date: 2014-01-13T14:56:25+00:00
author: 李朝
layout: post
guid: http://www.lizhaoblog.com/?p=27
permalink: /archives/27
seo_follow:
  - 'false'
seo_noindex:
  - 'false'
categories:
  - 虚拟化
tags:
  - kvm
  - libvirt
---
一、通过xml创建虚拟机，virsh define vm.xml.

vm.xml配置如下:

<pre class="brush: xml; title: ; notranslate" title="">&lt;domain type='kvm'&gt;
	&lt;name&gt;testvm&lt;/name&gt;&lt;!--虚拟机名称--&gt;
	&lt;memory&gt;1024000&lt;/memory&gt;&lt;!--在不修改配置不reboot的情况下，虚拟机的最大内存--&gt;
	&lt;currentMemory&gt;1024000&lt;/currentMemory&gt;
	&lt;!--虚拟机当前内存,一般小于等于memory的值，可以用virsh setmen 调整虚拟机内存，但不能大于memory设置的值。--&gt;
	&lt;vcpu&gt;1&lt;/vcpu&gt;&lt;!--cpu核数目--&gt;
	&lt;os&gt;
		&lt;type arch='x86_64' machine='pc'&gt;hvm&lt;/type&gt;
		&lt;boot dev='hd' /&gt;&lt;!--启动方式可以是 cdrom、hd--&gt;
	&lt;/os&gt;
	&lt;features&gt;
		&lt;acpi /&gt;
	&lt;/features&gt;
	&lt;clock offset='utc' /&gt;
	&lt;on_poweroff&gt;destroy&lt;/on_poweroff&gt;
	&lt;on_reboot&gt;restart&lt;/on_reboot&gt;
	&lt;on_crash&gt;destroy&lt;/on_crash&gt;
	&lt;devices&gt;
		&lt;emulator&gt;/usr/bin/kvm&lt;/emulator&gt;
		&lt;disk type='file' device='disk'&gt;
			&lt;driver name='qemu' type='qcow2' /&gt;&lt;!--镜像文件类型：qcow2、raw 。。。--&gt;
			&lt;source file='/mnt/testlz/ubuntu1204.img' /&gt;&lt;!--镜像地址--&gt;
			&lt;target dev='vda' bus='virtio' /&gt;&lt;!--指定硬盘号，virtio优化虚拟机IO--&gt;
			&lt;address type='pci' domain='0x0000' bus='0x00' slot='0x04' function='0x0' /&gt;
		&lt;/disk&gt;
		&lt;interface type='bridge'&gt;&lt;!--网络连接方式：birdge,network--&gt;
			&lt;source bridge='br0' /&gt;&lt;!--网桥名称,如果连接方式为network 则把birdge改为network，br0改为对应的网络--&gt;
			&lt;model type='virtio' /&gt;
			&lt;address type='pci' domain='0x0000' bus='0x00' slot='0x03' function='0x0' /&gt;
		&lt;/interface&gt;
		&lt;input type='tablet' bus='usb' /&gt;
		&lt;input type='mouse' bus='ps2' /&gt;
		&lt;graphics type='vnc' port='5900' autoport='yes' listen='0.0.0.0'&gt;
			&lt;!--vnc端口,autoport为yes时，端口号会自动分配（如果被占用则自动向后+1）--&gt;
			&lt;listen type='address' address='0.0.0.0' /&gt;
		&lt;/graphics&gt;
		&lt;video&gt;
			&lt;model type='cirrus' vram='9216' heads='1' /&gt;
			&lt;address type='pci' domain='0x0000' bus='0x00' slot='0x02'function='0x0' /&gt;
		&lt;/video&gt;
		&lt;memballoon model='virtio'&gt;
			&lt;address type='pci' domain='0x0000' bus='0x00' slot='0x05' function='0x0' /&gt;
		&lt;/memballoon&gt;
	&lt;/devices&gt;
&lt;/domain&gt;
</pre>

如果要从cdrom启动则增加如下代码：

<pre class="brush: xml; title: ; notranslate" title="">&lt;disk type='file' device='cdrom'&gt;
	&lt;driver name='qemu' type='raw' /&gt;
	&lt;source file='/opt/lizhao/ubuntu-12.04-server-amd64.iso' /&gt;
	&lt;!-- 光盘iso文件地址 --&gt;
	&lt;target dev='hdc' bus='ide' /&gt;
	&lt;readonly /&gt;
	&lt;alias name='ide0-1-0' /&gt;
	&lt;address type='drive' controller='0' bus='1' target='0' unit='0' /&gt;
&lt;/disk&gt;
</pre>

二、通过命令行创建虚拟机，命令如下：

<pre class="brush: bash; title: ; notranslate" title="">virt-install --name=testVM\    #虚机名称
--ram=1024 \  #内存大小
--vcpus=1 \    #cpu核数
--boot=cdrom \   #启动方式
--os-type linux \   #系统类型
--os-variant ubuntuprecise \   #系统版本标识，可用 virsh --os-variant list 查看所有系统版本列表
--disk path=/opt/lizhao/lizhao.qcow2,device=disk,cache=writeback,format=qcow2,bus=virtio \  #镜像文件地址，如果是qcow2的格式，必须指定cache=writeback,format=qcow2，否则会出现读取大小错误，virtio,io加速
--cdrom=/opt/lizhao/ubuntu-12.04-server-amd64.iso  \  #光驱文件地址
--bridge=br0,model=virtio \   #网络连接类型，桥接、nat
--vnc --vncport=5900 --vnclisten=0.0.0.0 \ #vnc
--accelerate \   #kvm加速
--hvm \
--noautoconsole
</pre>