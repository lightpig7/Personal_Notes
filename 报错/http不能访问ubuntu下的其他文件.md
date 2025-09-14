
vim /etc/php/8.1/apache2/php.ini

将以下Off改为On

```
display_errors =On
display_startup_errors =On
```
重启apache2服务
`sudo service apache2 restart`