# OBFS-HTTP

监听器名称: `ohttp`

OBFS监听器根据服务配置，监听在指定TCP端口，并使用HTTP协议进行握手。

=== "命令行"
    ```
	gost -L http+ohttp://:8443
	```
=== "配置文件"
    ```yaml
	services:
	- name: service-0
	  addr: ":8443"
	  handler:
		type: http
	  listener:
		type: ohttp
	```

## 参数列表

`header`
:    自定义HTTP响应头