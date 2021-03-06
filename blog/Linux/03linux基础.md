# Linux基础知识

## umask

>   当用户的`UID`小于199，并且用户名和用户组名相同时，`umaks`的值为`022`。否则的话`umask`的值为`002`，当创建文件夹时默认的权限为`0777 - umask`，当创建文件时默认的权限为`0777 - umaks - 0111`，文件默认取消执行权限，因为执行权限对文件影响大
>
>   配置文件
>
>   *   系统配置文件：`/etc/profile`
>   *   `shell`配置文件：`/etc/bashrc`
>   *   某一个用户的`shell`配置文件：`~/.bashrc`
>   *   使配置文件生效：`source 要生效的文件`，即让系统重新读取该文件，否则需要内核自举。

## 特殊权限

>   *   粘滞位(`sticky`)
>       *   只针对目录生效，当一个目录有`sticky`权限时，在这个目录中的文件只能被文件的所有者删除。
>       *   设置：`chmod o+t 目录`或`chmod 1xxx 目录`
>   *   强制位(`sgid`)
>       *   对文件作用：只针对二进制可执行文件，当文件有`sgid`时，任何人执行此文件产生的进程都属于文件的组
>       *   对目录：当目录有`sgid`时，任何人在此目录中建立的文件都属于目录。
>       *   `chmod g+s 二进制文件/目录`或`chmod 2xxx 二进制文件/目录`
>   *   冒险位(`suid`)
>       *   对文件，任何人执行二进制文件，进程都属于进程的所有人。对文件的权限进行降级操作，避免`rm -rf /*`的误操作，对`rm`文件的权限进行降级。
>       *   `chmod u+s 文件`或`chmod 4xxx 文件`

## acl权限

>   学会使用`man setfcal`
>
>   设置：`setfcal -m u:username:rwx dir`
>
>   设置：`setfcal -m g:groupname:rwx dir`
>
>   查看：`getfacl filename`
>
>   作用：让特定的用户对特定的文件拥有特定的权限
>
>   acl列表查看：
>
>   ​	权限位`+`号表示acl权限开启
>
>   查看文件acl开启的权限：`getfacl 文件命名`
>
>   ```
>   # file:文件名称
>   # owner:文件所有者
>   # group:文件所属组
>   use::所有者权限
>   user:用户:指定用户的权限
>   group::文件所有组的权限
>   maks：:能赋予用户最大的权限
>   other::其他用户
>   ```
>
>   *   `mask`值
>
>       >   在权限列表中mask表示能生效的权利值，当用`chmod`减少开启acl的文件权限后，`mask`值会发生变化
>
>   *   恢复mask值
>
>       >   `setfacl -m m:rwx filename`
>
>   *   acl默认权限
>
>       >   只针对目录设定，且只针对设定完之后新建的文件或目录生效，不会对已存在的文件生效
>       >
>       >   `setfacl -m d:u:username:rwx dir`

## 系统进程及服务的控制

>   *   图形查看进程
>
>   *   命令查看(`ps`)
>
>       *   `-A`：显示所有进程
>
>       *   `-a`：当前环境中运行的进程，不显示管理信息与当前终端的进程信息
>
>       *   `-u`：有效使用者和相关的进程
>
>       *   `a`：当前环境中的进程，包含管理信息与当前终端的进程信息
>
>       *   `x`：通常与`-a`参数一起使用，列出系统中所有运行包含`tty`输出设备
>
>       *   `-f`：显示进程父子关系
>
>       *   `-e`：经常与`-f`一起使用，显示详细信息，显示系统的资源调用和进程的详细信息
>
>       *   `-o`：显示指定信息
>
>           *   `comm`：显示命令
>           *   `user`：进程所有用户
>           *   `group`：进程所有组
>           *   `%cpu`：进程cpu使用率
>           *   `%mem`：进程使用内存使用率
>           *   `pid`：进程id号
>           *   `nice`：查看优先级
>           *   `stat`：状态
>
>           >例：`ps  -o comm,user,group,%cpu,%mem,pid,nice`，不要加多余的空格
>
>   *   进程排序
>
>       *   `ps ax --sort=+%cpu`
>
>           *   `+%cpu`：按照CPU使用率正序输出，小 ---> 大
>           *   `-%cpu`：按照CPU使用率反序输出，大 ---> 小
>
>           >   也可以使用`mem`进行排序。
>
>   *   进程优先级：范围[-20, 19]，数值越小优先级越高。
>
>       *   `S`：表示进程状态
>       *   `l`：内存中有锁定空间
>       *   `N`：表示优先级低
>       *   `<`：表示优先级高
>       *   `+`：表示前台运行
>       *   `s`：顶级进程
>
>   *   修改优先级
>
>       *   `renice -n -5 pid`，修改进程pid的优先级为`-5`
>       *   `renice -n -5 程序`，开启进程并设置优先级为`-5`
>
>   *   进程前后台调用
>
>       *   `Ctrl + z`：把占用终端的进程切入后台，释放终端
>       *   `jobs`：查看被打入环境后台的进程
>       *   `bg job号`：把后台程序运行到后台，不占用终端
>       *   `fg job号` ：把程序运行到前台，占用终端
>       *   `fg`：调用优先`+` > `-`>`什么都么有`
>       *   在程序最后加`&`，进程直接后台运行
>
>   *   进程信号：`kill -signal pid`
>
>       >   `signal`信号类型
>
>       *   `1`：进程重新加载配置
>       *   `2`：删除进程在内存中的数据
>       *   `3`：删除鼠标在内存中的数据，`Ctrl + \`
>       *   `9`：强行结束单个进程(不可被阻塞)
>       *   `15`：正常关闭进程(可能会被阻塞)
>       *   `18`：运行暂停的进程
>       *   `19`：暂停某个进程(不能被阻塞)
>       *   `20`：把进程打入后台(可以被阻塞)
>
>       >   例：`kill -9 pid`，使用`kill -l`查看所有信号
>       >
>       >   `killall -9 程序名`，根据程序名，批量关闭程序
>       >
>       >   `pkill -u student -9`，根据条件发出信号，踢出用户
>
>   *   常用组合
>
>       *   `ps ef`：显示进程详细信息并显示进程父子关系
>       *   `ps aux`：显示系统中所有进程和显示进程用户
>       *   `ps ax`：显示当前系统中的所有进程
