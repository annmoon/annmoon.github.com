---
layout: post
title: "File permission"
description: ""
category: linux
tags: [file]
---

- 리눅스 시스템에서 ls -l 명령을 실행하면 해당 디렉토리의 파일과 디렉토리 리스트가 나온다.
{% highlight bash %}
[root@ServerA ~]# ls -l
total 92
-rw-------. 1 root root  1457 Nov 12  2013 anaconda-ks.cfg
drwxr-xr-x. 2 root root  4096 Nov 11 23:32 Desktop
drwxr-xr-x. 2 root root  4096 Nov 11 23:32 Documents
drwxr-xr-x. 2 root root  4096 Nov 11 23:32 Downloads
-rw-r--r--. 1 root root 40392 Nov 12  2013 install.log
-rw-r--r--. 1 root root  9936 Nov 12  2013 install.log.syslog
drwxr-xr-x. 2 root root  4096 Nov 11 23:32 Music
drwxr-xr-x. 2 root root  4096 Nov 11 23:32 Pictures
drwxr-xr-x. 2 root root  4096 Nov 11 23:32 Public
drwxr-xr-x. 2 root root  4096 Nov 11 23:32 Templates
drwxr-xr-x. 2 root root  4096 Nov 11 23:32 Videos
{% endhighlight %}

- Downloads 디렉토리와 install.log 파일을 가지고 의미를 살펴보자.

![](/images/post/owner.jpg)

**1 - 파일유형**

d 일 경우는 디렉토리, 일반적인 파일일 경우 - 로 표시가 된다. b는 블록디바이스, c는 문자 디바이스, l 은 링크이다. 
즉, Downloads 는 디렉토리, install.log 는 파일이라는 의미이다.


**2 - 허가권 (Permission)**

파일 permission 은 3개씩 끊어서 읽으면 된다. r 은 read, w 는 write, x 는 execute 로, rw- 에 의미는 읽고 쓸수는 있지만, 실행 권한은 없다는 것을 의미한다. 
첫 번째 3개는 소유자 (user)의 접근권한, 두 번째는 그룹(group) 의 접근권한, 마지막 3개는 그 외의 사용자의 접근권한을 의미한다. 
위에서 Downloads 디렉토리는 소유자는 읽고/쓰고/실행 할수 있지만, 그룹과 그 이외의 사용자들은 읽고 실행할 수만 있다. (쓰기 권한은 없다)
install.log 파일 같은 경우는 소유자는 읽거나 쓸 수 있고, 그룹과 그 이외의 사용자들은 읽을 수만 있다. 
permission 은 숫자로도 표현이 가능하다. 소유자/그룹/그 외 사용자 에 각각의 3자리(rwx)를 4-2-1 로 표현을 한다면,  rwxr-xr-x 같은 경우 각 자리수를 더해서 755 로 표현할 수 있다. (- 는 0 으로 생각하면 된다.) 
즉,  rwx는 4,2,1 이므로 7 / r-x 는 4,0,1 이므로 5  가 된다.
파일 및 디렉토리 허가권은 chmod 명령으로 변경 가능하다. 예를 들어, 
{% highlight bash %}
[root@ServerA ~]# chmod 777 install.log 
{% endhighlight %}
란 명령을 실행하면 해당 파일 permission 이 rwxrwxrwx 로 변한 것을 확인 할 수 있을 것이다.


**3 - 링크수**

파일의 링크에는 하드 링크 (Hard Link) 와 심볼릭 링크 (Symbolic or Soft Link) 가 있다. 
하드 링크는 원본 파일과 같은 inode 를 사용하게 되며 ln 원본파일 링크파일 명령어로 생성할 수 있다.
{% highlight bash %}
[root@ServerA ~]# ln install.log hardlink
{% endhighlight %}
하드 링크의 경우 원본 파일이 없어져도 아무 이상이 없다.
심볼릭 (또는 소프트) 링크는 윈도우의 바로가기 같은 역활을 한다. 소프트 링크를 생성하면 원본과 다른 새로운 inode 를 만들고 데이터는 원본 파일을 연결하게 되는 효과를 갖는다. 아래와 같이 만들 수 있다.
{% highlight bash %}
[root@ServerA ~]# ln -s install.log softlink
{% endhighlight %}
소프트 링크의 경우 원본 파일이 없어지면 연결이 끊어진다. 하지만 다시 원본을 생성하면 다시 링크가 복구 된다.


**4 - 소유자 이름**

파일 소유자의 이름을 의미한다. 둘 다 root 유저가 소유자 이다.


**5 - 소유 그룹 이름**

파일을 소유한 그룹을 의미한다. 둘 다 root 그룹이다.


**6 - 크기 (byte)**

**7 - 마지막 변경 날짜시간**

**8 - 이름**
