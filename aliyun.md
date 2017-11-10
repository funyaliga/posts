##个人阿里云ESC填坑记录##	

系统: CentOS 7.2 64位



### FTP ###

参考链接:  https://help.aliyun.com/document_detail/51998.html

1.安装

 `yum install -y vsftpd`  // 安装vsftpd

2.配置

`/etc/vsftpd/vsftpd.conf` //主配置文件，核心配置文件

`/etc/vsftpd/ftpusers` //黑名单，这个里面的用户不允许访问FTP服务器

`/etc/vsftpd/user_list` //白名单，允许访问FTP服务器的用户列表

3.启动

`systemctl enable vsftpd.service` //设置开机自启动

`systemctl start vsftpd.service` //启动ftp服务

`systemctl restart vsftpd.service` // 重启ftp服务

`netstat -antup | grep ftp` //查看ftp服务端口

4.添加用户

`useradd 用户名 -d /var/www/html -g ftp -s /sbin/nologin ` // 创建用户 

`passwd 用户名` // 修改用户密码

`userdel 用户名` // 删除用户

`cat /etc/passwd` // 查看用户组

`chown -R 用户名.root /var/www/html` 修改权限

修改/etc/vsftpd/vsftpd.conf
`anonymous enable=NO`
`local_enable=YES`

一些坑：

1. ftp显示“无法连接到服务器” / telnet ip 端口  -> 连接失败

   解决方法：在阿里云安全组配置-添加安全组规则-添加21端口

2. ftp显示“530 Permission denied ”
   解决方法：可能在userlist_file 里面禁用了用户，删掉

3. ftp显示“服务器发回了不可路由的地址。使用服务器地址代替。”

   解决方法

   1. Filezilla-编辑-设置-连接-FTP-被动模式， 将“使用服务器的外部ip地址来代替”改为“退回到主动模式”
   2. 在阿里云安全组配置-添加安全组规则-1024/65535， 把所有端口开放（不建议）
   3. 在vftpd.conf 开启pasv模式，见下面其他配置最后3行

   ​

其他配置

参考链接： https://segmentfault.com/a/1190000010526905

```nginx
local_root=/ftpfile # (当本地用户登入时，将被更换到定义的目录下，默认值为各用户的家目录)
anon_root=/ftpfile # (使用匿名登入时，所登入的目录)
use_localtime=YES # (默认是GMT时间，改成使用本机系统时间)
anonymous_enable=NO # (不允许匿名用户登录)
local_enable=YES # (允许本地用户登录)
write_enable=YES # (本地用户可以在自己家目录中进行读写操作)
local_umask=022 # (本地用户新增档案时的umask值)
dirmessage_enable=YES # (如果启动这个选项，那么使用者第一次进入一个目录时，会检查该目录下是否有.message这个档案，如果有，则会出现此档案的内容，通常这个档案会放置欢迎话语，或是对该目录的说明。默认值为开启)
xferlog_enable=YES # (是否启用上传/下载日志记录。如果启用，则上传与下载的信息将被完整纪录在xferlog_file 所定义的档案中。预设为开启。)
connect_from_port_20=YES # (指定FTP使用20端口进行数据传输，默认值为YES)
xferlog_std_format=YES # (如果启用，则日志文件将会写成xferlog的标准格式)
ftpd_banner=Welcome to HelloWord FTP Server # (这里用来定义欢迎话语的字符串)

chroot_local_user=NO # (用于指定用户列表文件中的用户是否允许切换到上级目录)
chroot_list_enable=YES # (设置是否启用chroot_list_file配置项指定的用户列表文件)
chroot_list_file=/etc/vsftpd/chroot_list # (用于指定用户列表文件)
listen=YES # (设置vsftpd服务器是否以standalone模式运行，以standalone模式运行是一种较好的方式，此时listen必须设置为YES，此为默认值。建议不要更改，有很多与服务器运行相关的配置命令，需要在此模式下才有效，若设置为NO，则vsftpd不是以独立的服务运行，要受到xinetd服务的管控，功能上会受到限制)
pam_service_name=vsftpd # (虚拟用户使用PAM认证方式，这里是设置PAM使用的名称，默认即可，与/etc/pam.d/vsftpd对应) userlist_enable=YES(是否启用vsftpd.user_list文件，黑名单,白名单都可以
allow_writeable_chroot=YES  # 如果启用了限定用户在其主目录下需要添加这个配置
pasv_min_port=61001 # (被动模式使用端口范围最小值)
pasv_max_port=62000 # (被动模式使用端口范围最大值)
pasv_enable=YES 
# 若设置为YES，则使用PASV工作模式；若设置为NO，则使用PORT模式。默认值为YES，即使用PASV工作模式。
# FTP协议有两种工作方式：PORT方式和PASV方式，中文意思为主动式和被动式。
# 一、PORT（主动）方式的连接过程是：客户端向服务器的FTP端口（默认是21）发送连接请求，服务器接受连接，建立一条命令链路。 
# 当需要传送数据时，客户端在命令链路上用 PORT命令告诉服务器：“我打开了****端口，你过来连接我”。于是服务器从20端口向客户端的****端口发送连接请求，建立一条数据链路来传送数据。
# 二、PASV（被动）方式的连接过程是：客户端向服务器的FTP端口（默认是21）发送连接请求，服务器接受连接，建立一条命令链路。 
# 当需要传送数据时，服务器在命令链路上用 PASV命令告诉客户端：“我打开了****端口，你过来连接我”。于是客户端向服务器的****端口发送连接请求，建立一条数据链路来传送数据。 
# 从上面可以看出，两种方式的命令链路连接方法是一样的，而数据链路的建立方法就完全不同。而FTP的复杂性就在于此。
```



### nginx ###

yum 和 源码包 安装的 区别 :  https://segmentfault.com/a/1190000007116797

##### yum#####

`yum -y install nginx`  // 安装

`rpm -ql nginx` // 查看安装目录

`rpm -e nginx` // 卸载

`rpm -e --nodeps nginx` // 强制卸载

`service nginx start` // 启动

`service nginx stop` // 停止

`service nginx restart` // 重启

`service nginx reload` // 重新加载配置文件

`systemctl enable nginx.service`  // 开机启动

##### 源码包 #####

1.安装准备

```
yum install gcc-c++  
yum install pcre pcre-devel  
yum install zlib zlib-devel  
yum install openssl openssl--devel 
```

2.安装

```
cd /usr/local  // 进入安装目录
wget -c https://nginx.org/download/nginx-1.12.2.tar.gz   // 下载最新版nginx
tar -zxvf nginx-1.12.1.tar.gz    //解压

cd  nginx-1.12.1 
./configure
make
make install
```

3.启动及相关

```
cd /usr/local/nginx/sbin/
./nginx
```

4.重启

```
./nginx -s quit
./nginx
```

5.重新加载配置文件

```
./nginx -s reload
```



##### 多域名管理 #####

```
vi nginx.conf   // 源码包方式时，nginx.conf在nginx/conf/nginx.conf

include vhost/*.conf;  // 添加这行，引入vhost文件夹下所有conf文件，便于后期维护

mkdir vhost // 创建vhost文件夹

vi xxx.com.conf // 创建文件

server{
  listen   80;                  // 端口
  server_name  xxx.com;         // 绑定域名。
  index index.html index.php;   // 指定默认文件。
  root /data/www;           	// 指定网站根目录。
  error_page 404   /404.html;
}
```



###nodejs###

##### 安装nvm #####

1. 直接从github拷贝那个安装命令就好

```
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.6/install.sh | bash
```

2. 重启
3. 查看是否安装成功 `command -v nvm`  
4. 查看可安装的node版本 `nvm ls-remote`
5. 安装某版本的node`nvm install 版本号  `
6. 使用某版本的node `nvm use 版本号` 
7. 开启启动某版本的node `nvm alias default 版本号`
8. 看是否安装成功`npm -v` `node -v`  



### LAMP ###

https://lamp.sh/install.html



### nginx  apache共存 ###

1. 修改apache端口号
   打开 /usr/local/apache/conf/httpd.conf， 把`Listen 80` 改成其他端口号，比如`Listen 8080`

2. 修改nginx配置
   打开nginx.conf，添加配置

   ```
   server {
   	listen 80;
   	server_name x.xxx.xx;
   	location / {
   		proxy_set_header X-Real-IP  $remote_addr;
   		proxy_set_header X-Forwarded-For $remote_addr;
   		proxy_set_header Host $host;
   		proxy_pass http://127.0.0.1:8080;
   	}
   }

   ```

   ​

