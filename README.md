# HF-yuan-pay
HF源支付程序源码
https://hfz.pw/archives/1470


一、环境安装
	系统要求centos7.x
	Nginx1.20 + PHP 7.2 + Mysql 5.6+Redis6.27+Supervisor 管理器

二、准备工作
	准备好三个已经解析好的域名，分别为前台域名，后台域名，云端域名。
	服务器和宝塔放行 82 和 5000 端口 

三、安装云端
	①终端命令（命令行）：
	1.sudo rpm -Uvh https://packages.microsoft.com/config/rhel/7/packages-microsoft-prod.rpm
	2.完成后输入：sudo yum install dotnet-sdk-3.1  【中途会有两次提示你输入，全部输入 y 回车即可】

	②新建一个纯静态网站，填写云端域名。将云端文件上传并解压

	③创建云端进程
	进入软件商店，打开 Supervisor 管理器，添加守护进程，名称cloud，运行目录设置为云端网站路径
	启动命令：dotnet XMS.WeChat.Api.dll

四、创建后台网站
	将后台源码上传解压，运行目录为 Public 伪静态：Thinkphp ，打开网站进行安装，安装完成
	

六、搭建前台网站
	新建一个纯静态网站，将前台文件上传并解压，appsettings.json 文件内的数据库信息填写后台的数据库链接，
	云端密钥信息填写如下：
	"CloudUrl": "http://云端域名:82",
	"CloudKey": " ", 
	"PayUrl": "http://ifengling.cn",更改为你的后台域名
	"APIURL": "http://pay.ifengling.cn"更改为你的前台域名

七、创建前台进程
	进入软件商店，打开 Supervisor 管理器，添加守护进程，名称pay，运行目录设置为前台网站路径，启动命令为：dotnet FrontPay.dll

八、设置好前台反向代理 目标 URL 为【 http://你的域名:5000 】发送域名为$host

九、验证码显示
	将服务器根目录下 /etc/pki/tls/cert.pem 复制到/usr/local/openssl 然后重启一下守护进程
	再执行以下命令
	yum install -y libgdiplus-2.10-10.el7.x86\_64




十、安装监控
宝塔计划任务

N分钟   1分钟

#!/bin/bash 
step=10 #间隔的秒数，不能大于60        
for (( i = 0; i < 60; i=(i+step) )); do      
    curl  -sS --connect-timeout 10 -m 60 'https://前台域名/API/GeettingAsynAllPayCron'      
    echo "----------------------------------------------------------------------------"      
endDate=`date +"%Y-%m-%d %H:%M:%S"`      
echo "★\\\\\\\[$endDate\\\\\\\] Successful"      
echo "----------------------------------------------------------------------------"      
      
sleep $step      
done      
exit 0
