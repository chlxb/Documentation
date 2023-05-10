# curl简介及语法

curl是一个命令行访问URL的工具，作用是发出网络请求，然后得到和提取数据，显示在"标准输出"（stdout）上面。可以用它构造http request报文，且可以解析服务器返回的http response，额外还支持cookie特性，可以用curl完成web浏览器的基本功能，curl还支持HTTPS/FTP/FTPS/TELNET/LDAP等协议。

# 常用选项：

```
curl --help all 查看所有帮助信息
Usage: curl [options...] <url>
 -d, --data <data>   HTTP POST data，以post方式传送数据
 -G, --get 以get的方式发送数据
 -f, --fail          Fail silently (no output at all) on HTTP errors,连接失败时不显示http错误
 -h, --help <category>  Get help for commands
 -i, --include       Include protocol response headers in the output
 -o, --output <file>  Write to file instead of stdout, 写文件代替标准输出
 -O, --remote-name   Write output to a file named as the remote file，把输出写到该文件中，保留远程文件的文件名
 -S, --show-error    Show error even when -s is used
 -s, --silent        Silent mode,不输出任何信息
 -T, --upload-file <file>  Transfer local FILE to destination
 -u, --user <user:password>  Server user and password
 -A, --user-agent <name>  Send User-Agent <name> to server
 -v, --verbose       Make the operation more talkative,详细输出，包含请求和响应的首部
 -V, --version       Show version number and quit
 -L 跟随重定向
 -c 保存服务器的cookie文件
 -x ip:port 指定使用的http代理
 -H header:value 为HTTP请求设置任意header及值
 -C 在保存文件时进行续传
```


# 参考
[curl 命令 阮一峰](https://www.ruanyifeng.com/blog/2019/09/curl-reference.html)