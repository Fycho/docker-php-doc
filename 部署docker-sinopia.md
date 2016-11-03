#在docker容器里部署sinopia服务器

 配置docker-compose.yml并更新容器
> $ docker up -d

 命令docker stop/restart <容器名> 停止、重启sinopia容器
 将本地npm源指向sinopia，
> $ npm set repository <主机地址>:4873
 
#config.yml字段说明
- 将max_users设置为-1以避免登录的终端自行add npm user
- 禁用add user后通过修改htpasswd来增加用户，加密方式为SHA1哈稀之后转换成Base64输出
- 修改files来指定密码验证文件
- packages里来修改包的获得与发布的权限
- uplinks配置上游npm源，当私有npm里不存在相应包时，npm install的时候会去上游源搜索，国内一般用淘宝的源
- 将listen设置为docker-compose里当前容器在整个docker局域网的的IP，端口为4873
