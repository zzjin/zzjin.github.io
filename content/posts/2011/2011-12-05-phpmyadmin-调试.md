---
date: 2011-12-05
categories:
  - web
  - 个人
tags:
  - phpmyadmin
  - web
  - 记录
title: phpmyadmin 调试
---

今天下午花了点时间更新了一下强大的phpmyadmin软件~

最新的版本是3.4.8~php写的就是好~直接下下来覆盖原来的文件就好了~

新版本的ui风格很不错啊~用到了很多的ajax特效~特别赞.看着赏心悦目的话用起来也非常畅快流离啊~

截图放在这:

[singlepic id=771 w=640 h=480 float=center]

不过在开启phpmyadmin的高级功能的时候出了点小问题~

就是按照官方的一个脚本把新的pma用户加上之后.发现还是提示没有配置好...

仔细的对了整个官方的额文档和我的本地数据库之后发现pma用户的权限还少了一个东西...

官方的配置脚本是:

[sql]

GRANT USAGE ON mysql.* TO 'pma'@'localhost' IDENTIFIED BY 'pmapass';

GRANT SELECT ( Host, User, Select_priv, Insert_priv, Update_priv, Delete_priv, Create_priv, Drop_priv, Reload_priv, Shutdown_priv, Process_priv, File_priv, Grant_priv, References_priv, Index_priv, Alter_priv, Show_db_priv, Super_priv, Create_tmp_table_priv, Lock_tables_priv, Execute_priv, Repl_slave_priv, Repl_client_priv ) ON mysql.user TO 'pma'@'localhost';

GRANT SELECT ON mysql.db TO 'pma'@'localhost';

GRANT SELECT ON mysql.host TO 'pma'@'localhost';

GRANT SELECT (Host, Db, User, Table_name, Table_priv, Column_priv) ON mysql.tables_priv TO 'pma'@'localhost';

GRANT SELECT, INSERT, UPDATE, DELETE ON <pma_db>;.* TO 'pma'@'localhost';
[/sql]

这样的问题就是这个新用户无法访问我们需要进行管理的phpmyadmin数据库..
所以在最后还得加上一句:

[sql]
GRANT ALL PRIVILEGES ON phpmyadmin.* TO 'pma'@localhost IDENTIFIED BY 'pmapass';
[/sql]

这样就可以正常的管理高级设置功能了~