# Troubleshooting

We collect the most common troubleshooting of using LNMP for your reference:

## Application related

#### Redirects Error?

Check your *.htaccess* file in your application root directory, remove cycle redirects settings

## Database related

#### Database service could not be started?

Insufficient disk space, insufficient memory, and configuration file errors can make database service could not be started  

It is recommended to first check through the command.

```shell
# restart mysql service
systemctl restart mysql

# view disk space
df -lh

# view memory rate
free -lh
```

#### The database log file is too large, resulting in insufficient disk space?

By default, mysql will automatically open the binlog. Binlog is mainly used to recover the database without backup. However, the binlog will take up a lot of space. If you don't clean it for a long time, the remaining disk space will be 0, which will affect the database or the server will not start.

If you have confidence in your own backup, you do not need the binlog function. Refer to the following steps to turn it off:

1. Edit [MySQL Configuration File] (/stack-components.md#mysql) and comment out the binlog log   
```
#log-bin=mysql-bin
```
2. Restart mysql
```
systemctl restart mysqld
```
#### phpMyAdmin page access blank?

Please try another browser, such as chrome or firefox. If the phpMyAdmin can be opened normally before, and now appears to be incomplete or blank, it is recommended to clean up the browser cache, cookies and other information

## Nginx related

#### When restart Nginx service, such error *No spaces...*

Do not worry, the Nginx service is running

## Instance related

Instance troubleshooting is closely related to the Instance provider that is Cloud Platform   
Please refer to [Cloud Platform Documentation](https://support.websoft9.com/docs/faq/tech-instance.html)

## Network related