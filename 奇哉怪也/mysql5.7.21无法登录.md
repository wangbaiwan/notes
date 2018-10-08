# mysql5.7.21无法登录的问题
<p>因为deepin升级到15.7发生无法进入系统的问题，只能重装了系统，所以很多原先装了软件都得重新装一遍。结果没想到，会遇上一些新问题。</p>
<p><code>sudo apt install mysql-server</code><br>
使用apt安装mysql，但是直到安装完成都没有出现设置密码的引导，有些意外</p>
<p><code>mysql -u root -p</code><br>
果然，无法登录。</p>
<p>看官方的文档说，生成的随机密码在error.log里<br>
<code>cat /var/log/mysql/error.log</code><br>
却发现</p>

> 2018-09-28T07:07:21.007356Z 1 [Warning] root@localhost is created with an empty password ! Please consider switching off the --initialize-insecure option.

<p>也就是说生成的root是没有密码的，尝试登录，依旧失败<br>
<code>sudo systemctl stop mysql</code><br>
<code>sudo mysqld_safe --skip-grant-tables</code><br>
尝试修改root密码，但还是没有效果</p>
<p>无意中看到一个帖子<br>
<code>SELECT user,plugin FROM mysql.user</code></p>

> +------------------+-----------------------+
| user             | plugin                |
+------------------+-----------------------+
| root             | auth_socket |
| mysql.session    | mysql_native_password |
| mysql.sys        | mysql_native_password |
| debian-sys-maint | mysql_native_password |
+------------------+-----------------------+
4 rows in set (0.00 sec)
<p>这其中只有root是使用的auth_socket，将其改为mysql_native_password<br>
<code>UPDATE mysql.user SET plugin = 'mysql_native_password' WHERE user = 'root'</code><br>
修改完以后就能正常使用了
</p>




