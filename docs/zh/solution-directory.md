# 迁移网站

迁移网站就是将**网站数据**移动到新的位置，然后通过配置，保证移动后可正常访问。

迁移是需要谨慎对待的操作，迁移之前需要清楚的明白如下要点：

- 被移动的网站数据对象：网站源码文件和数据库数据文件  
- 目的地位置：服务器目录之间转移（本地）和转移到外部服务器（外部）

被迁移对象和目的地位置的组合，形成了多种多样的迁移场景。下面详细说明最常见的迁移场景：

## 迁移网站源码（本地）

以将原目录 */data/wwwroot* 下的 **mysite1** 迁移到 */data2/wwwroot* 目标目录下为例，具体步骤如下：

1. 使用 WinSCP 连接服务器
2. 将 ***mysite1*** 文件夹整体拷贝到目标位置 */data2/wwwroot*
   ![](https://libs.websoft9.com/Websoft9/DocsPicture/zh/lamp/lamp-copysite1todata2-websoft9.png)
3. 修改 *default.conf* 中 mysite1 这个网站对应的 server{ } 配置段 root 项的值
   ![](https://libs.websoft9.com/Websoft9/DocsPicture/en/lnmp/lnmp-modifyvhostdata2-websoft9.png)

   原地址：/data/wwwroot/mysite1  
   目标地址：/data2/wwwroot/mysite1

4. 保存 default.conf，然后在 WinSCP 中运行重启服务命令 或 云控制台重启服务器 ：
      ~~~
      # 重启Nginx服务命令
      sudo systemctl restart nginx
      ~~~
5. 测试迁移后的结果，成功后可以删除原来的 *mysite1* 文件夹

## 迁移数据库文件（本地）

没有特殊情况，我们不建议迁移数据库文件到服务器上另外一个目录，毕竟主流的云厂商磁盘均可扩容。

如果要更改 [MySQL 数据库文件目录](/zh/stack-components.md#mysql)，请参考此处[ MySQL 专题文档](https://support.websoft9.com/docs/mysql/zh/solution-modifydatadir.html)

## 将/data目录迁移到数据盘（本地）

默认情况下 /data 是在系统盘的，当需要转移到数据盘，步骤如下:

1. 开始操作之前，**请务必做好数据备份**；
2. 提前购买数据盘，然后到云控制台将数据盘关联到云服务器
3. 连接服务器，将数据盘分区格式化
4. 在云服务器根目录下创建一个临时目录 temp 
5. 将数据盘挂载（mount）到:*/temp* 目录
4. 停止云服务器上的 Nginx 和 MySQL 服务
    ~~~
    sudo systemctl stop nginx mysqld
    ~~~

5. 将当前 */data* 下所有文件拷贝到服务器临时文件夹 */temp*  中
    > 数据较大的话，拷贝可能会失败，此步骤具体问题需具体对待
6. 等待数据转移完成
7. 连接服务器，将数据盘再次挂载（mount）到:*/data* 目录 
8. 运行以下命令重新启动 Nginx 和 MySQL:
   ```
   sudo systemctl start nginx mysqld
   ``` 
9. 测试迁移结果

> 数据盘格式化以及挂载（mount）操作非常复杂，需要有熟练的相关技能


## 迁移到外部服务器

网站从一台服务器（原服务器）迁移到另外一台服务器（目的服务器）是一个系统工程，基本步骤如下：

1. 通过云控制台，在目的服务器上[部署](/zh/stack-deployment.md)参数一致的 LNMP 镜像。
2. 通过 WinSCP 将原服务器上的网站源文件**下载**到本地电脑，然后再**上传**到目的服务器。
3. 通过 phpMyAdmin **导出**原服务器上的数据库，然后在目的服务器上**导入**数据库。
4. 把原服务器上的 default.conf 配置文件内容，完整拷贝到目的服务器的 default.conf 中，保存之。
5. 重启 Nginx 服务。
5. 解析域名到目的服务器，等待域名解析生效。
5. 通过域名访问网站，测试可用性。
6. 正式发布。

如果一台服务器上有多个网站需要迁移，建议逐个迁移