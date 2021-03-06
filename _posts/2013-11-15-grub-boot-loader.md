---
layout: post
title: "GRUB boot loader"
description: ""
category: linux
tags: [grub]
---


GRUB 의 설정 파일은 /boot/grub/grub.conf 이며, /etc/grub.conf 은 링크파일이다. 

파일안의 내용을 살펴보자.

{% highlight bash %}
[root@ServerA ~]# cat /boot/grub/grub.conf 
# grub.conf generated by anaconda
#
# Note that you do not have to rerun grub after making changes to this file
# NOTICE:  You have a /boot partition.  This means that
#          all kernel and initrd paths are relative to /boot/, eg.
#          root (hd0,0)
#          kernel /vmlinuz-version ro root=/dev/sda7
#          initrd /initrd-[generic-]version.img
#boot=/dev/sda
default=0
timeout=5
splashimage=(hd0,0)/grub/splash.xpm.gz
hiddenmenu
title CentOS (2.6.32-358.el6.x86_64)
    root (hd0,0)
    kernel /vmlinuz-2.6.32-358.el6.x86_64 ro root=UUID=6d06c8ae-ae0f-41cd-bb7a-c22923bcb967 rd_NO_LUKS rd_NO_LVM LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto  KEYBOARDTYPE=pc KEYTABLE=us rd_NO_DM rhgb quiet
    initrd /initramfs-2.6.32-358.el6.x86_64.img
[root@ServerA ~]# 
{% endhighlight %}

"#" 으로 된 부분은 주석이므로 넘어가고, default 부분 부터 살펴보면, 

#### default=0

기본적으로 부팅되는 부트 엔트리의 순차번호다. 각각의 부트 엔트리는 'title' 로 시작하므로 이 grub.conf 파일의 부트 엔트리는 1개 뿐이다. 순번은 0,1,2... 으로 매겨진다. 

#### timeout=5

초기에 부팅될 때 5초간의 여유를 준다는 의미이다. 이 시간을 0으로 주면 부팅 할 때 부팅을 위한 대기 화면이 나오지 않는다.

#### splashimage=(hd0,0)/grub/splash.xpm.gz

GRUB 메뉴의 부팅 이미지 파일을 지정한 부분이다. hd0 는 0번째 (첫 번째) 하드디스크를 의미하고 (/dev/sda), 다음의 0 숫자는 0번째 (첫 번째) 파티션을 의미한다 (/dev/sda1) 

#### hiddenmenu

기본적으로는 GRUB 부트 로더를 보여주지 않는 설정이다. 삭제하면 GRUB의 부트 로더 화면이 처음에 보일 것이다. 

#### title CentOS (2.6.32-358.el6.x86_64)

"title" 은 부팅되는 부트 엔트리를 의미하며 그 뒤는 초기 화면에서 보이는 메뉴 이름이다. 

#### root (hd0,0) : /boot 파티션이 저장된 장치 (/dev/sda1) 을 의미한다. 

#### kernel /vmlinuz-2.6.32-358.el6.x86_64 ro root=UUID  ~~~ (생략) : 

kernel 뒤에 나오는 부분은 실제 부팅될 커널 파일이며 이 파일은 /boot  디렉토리에 존재한다. 

그 다음의 ro 부분은 일기 전용으로 루트 파티션을 마운트 하라는 의미이고, 

root=UUID 부분은 UUID (Universally Unique Idenrification) 로 디바이스 이름을 지정한 것이다. 이 UUID 는 /etc/fstab 파일에 써 있는 것과 일치할 것이다. 

#### initrd /initramfs-2.6.32-358.el6.x86_64.img :

initrd 이미지가 있는 파일 이름을 명시해 준다. 이 파일도 /boot 디렉토리에 들어 있다.
