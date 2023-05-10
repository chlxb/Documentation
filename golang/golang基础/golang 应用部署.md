# 应用部署
针对Go的应用程序部署，我们可以利用第三方工具来管理，第三方的工具有很多，例如Supervisord、upstart、daemontools等，这小节我介绍目前自己系统中采用的工具Supervisord。

## 1. daemon
目前Go程序还不能实现daemon，详细的见这个Go语言的bug：<http://code.google.com/p/go/issues/detail?id=227>，大概的意思说很难从现有的使用的线程中fork一个出来，因为没有一种简单的方法来确保所有已经使用的线程的状态一致性问题。

但是我们可以看到很多网上的一些实现daemon的方法，例如下面两种方式：

* MarGo的一个实现思路，使用Command来执行自身的应用，如果真想实现，那么推荐这种方案
```
d := flag.Bool("d", false, "Whether or not to launch in the background(like a daemon)")
if *d {
	cmd := exec.Command(os.Args[0],
		"-close-fds",
		"-addr", *addr,
		"-call", *call,
	)
	serr, err := cmd.StderrPipe()
	if err != nil {
		log.Fatalln(err)
	}
	err = cmd.Start()
	if err != nil {
		log.Fatalln(err)
	}
	s, err := ioutil.ReadAll(serr)
	s = bytes.TrimSpace(s)
	if bytes.HasPrefix(s, []byte("addr: ")) {
		fmt.Println(string(s))
		cmd.Process.Release()
	} else {
		log.Printf("unexpected response from MarGo: `%s` error: `%v`\n", s, err)
		cmd.Process.Kill()
	}
}
```

* 另一种是利用syscall的方案，但是这个方案并不完善：
```
package main
 
import (
	"log"
	"os"
	"syscall"
)
 
func daemon(nochdir, noclose int) int {
	var ret, ret2 uintptr
	var err uintptr
 
	darwin := syscall.OS == "darwin"
 
	// already a daemon
	if syscall.Getppid() == 1 {
		return 0
	}
 
	// fork off the parent process
	ret, ret2, err = syscall.RawSyscall(syscall.SYS_FORK, 0, 0, 0)
	if err != 0 {
		return -1
	}
 
	// failure
	if ret2 < 0 {
		os.Exit(-1)
	}
 
	// handle exception for darwin
	if darwin && ret2 == 1 {
		ret = 0
	}
 
	// if we got a good PID, then we call exit the parent process.
	if ret > 0 {
		os.Exit(0)
	}
 
	/* Change the file mode mask */
	_ = syscall.Umask(0)
 
	// create a new SID for the child process
	s_ret, s_errno := syscall.Setsid()
	if s_errno != 0 {
		log.Printf("Error: syscall.Setsid errno: %d", s_errno)
	}
	if s_ret < 0 {
		return -1
	}
 
	if nochdir == 0 {
		os.Chdir("/")
	}
 
	if noclose == 0 {
		f, e := os.OpenFile("/dev/null", os.O_RDWR, 0)
		if e == nil {
			fd := f.Fd()
			syscall.Dup2(fd, os.Stdin.Fd())
			syscall.Dup2(fd, os.Stdout.Fd())
			syscall.Dup2(fd, os.Stderr.Fd())
		}
	}
 
	return 0
}	
```

## 2 Supervisord
Supervisord 是用 Python 实现的一款非常实用的进程管理工具。supervisord会帮你把管理的应用程序转成daemon程序，而且可以方便的通过命令开启、关闭、重启等操作，而且它管理的进程一旦崩溃会自动重启，这样就可以保证程序执行中断后的情况下有自我修复的功能。

### 2.1 安装
Supervisord可以通过`sudo easy_install supervisor`安装，当然也可以通过Supervisord官网下载后解压并转到源码所在的文件夹下执行setup.py install来安装。

* 使用easy_install必须安装setuptools

打开http://pypi.python.org/pypi/setuptools#files，根据你系统的python的版本下载相应的文件，然后执行sh setuptoolsxxxx.egg，这样就可以使用easy_install命令来安装Supervisord。

### 2.2 配置
Supervisord默认的配置文件路径为/etc/supervisord.conf，通过文本编辑器修改这个文件，下面是一个示例的配置文件：

```
;/etc/supervisord.conf
[unix_http_server]
file = /var/run/supervisord.sock
chmod = 0777
chown= root:root

[inet_http_server]
# Web管理界面设定
port=9001
username = admin
password = yourpassword

[supervisorctl]
; 必须和'unix_http_server'里面的设定匹配
serverurl = unix:///var/run/supervisord.sock

[supervisord]
logfile=/var/log/supervisord/supervisord.log ; (main log file;default $CWD/supervisord.log)
logfile_maxbytes=50MB       ; (max main logfile bytes b4 rotation;default 50MB)
logfile_backups=10          ; (num of main logfile rotation backups;default 10)
loglevel=info               ; (log level;default info; others: debug,warn,trace)
pidfile=/var/run/supervisord.pid ; (supervisord pidfile;default supervisord.pid)
nodaemon=true              ; (start in foreground if true;default false)
minfds=1024                 ; (min. avail startup file descriptors;default 1024)
minprocs=200                ; (min. avail process descriptors;default 200)
user=root                 ; (default is current user, required if root)
childlogdir=/var/log/supervisord/            ; ('AUTO' child log dir, default $TEMP)

[rpcinterface:supervisor]
supervisor.rpcinterface_factory = supervisor.rpcinterface:make_main_rpcinterface

; 管理的单个进程的配置，可以添加多个program
[program:blogdemon]
command=/data/blog/blogdemon
autostart = true
startsecs = 5
user = root
redirect_stderr = true
stdout_logfile = /var/log/supervisord/blogdemon.log
```

### 2.3 Supervisord 管理
Supervisord安装完成后有两个可用的命令行supervisor和supervisorctl，命令使用解释如下：

* supervisord，初始启动Supervisord，启动、管理配置中设置的进程。
* supervisorctl stop programxxx，停止某一个进程(programxxx)，programxxx为[program:blogdemon]里配置的值，这个示例就是blogdemon。
* supervisorctl start programxxx，启动某个进程
* supervisorctl restart programxxx，重启某个进程
* supervisorctl stop all，停止全部进程，注：start、restart、stop都不会载入最新的配置文件。
* supervisorctl reload，载入最新的配置文件，并按新的配置启动、管理所有进程。