# 文件安全与权限

## 文件

![ls -l](imgs\ls -l.png)

当创建一个文件时,系统保存了有关该文件的全部信息,包括:

* 文件位置
* 文件类型
* 文件长度
* 哪位用户拥有该文件,哪些用户可以访问该文件
* i节点
* 文件的修改时间
* 文件的权限位

权限位分组

* 文件属主权限		前三位
	 同组用户权限 	中三位
	 其他用户权限		后三位

文件信息

* 2 	该文件硬链接数目
   root	文件的属主
   root 	文件的属主root所在的缺省组(组名root)
   4096	字节表示的文件长度
   Aug 23 16:11		文件更新时间
   CONFIG_DIR		文件名

## 文件类型

文件的七种类型

* `d`    目录
* `l`     连接符号(指向另一个文件)
* `s`    套接字文件
* `b`    块设备文件
* `c`    字符设备文件
* `p`    命名管道文件
* `-` 普通文件,或者说,不属于以上几种类型的文件

## 权限

![文件权限及含义](imgs\文件权限及含义.png)

## 改变权限位

### 符号模式

```shell
Usage: chmod [OPTION]... MODE[,MODE]... FILE...
  or:  chmod [OPTION]... OCTAL-MODE FILE...
  or:  chmod [OPTION]... --reference=RFILE FILE...
Change the mode of each FILE to MODE.
With --reference, change the mode of each FILE to that of RFILE.

  -c, --changes          like verbose but report only when a change is made
  -f, --silent, --quiet  suppress most error messages
  -v, --verbose          output a diagnostic for every file processed
      --no-preserve-root  do not treat '/' specially (the default)
      --preserve-root    fail to operate recursively on '/'
      --reference=RFILE  use RFILE's mode instead of MODE values
  -R, --recursive        change files and directories recursively
      --help     display this help and exit
      --version  output version information and exit

Each MODE is of the form '[ugoa]*([-+=]([rwxXst]*|[ugo]))+|[-+=][0-7]+'.
```

```shell
# chmod命令的一般格式:
chmod [who] operator [permission] filename
# who的含义是:
# u 文件属主权限
# g 同组用户权限
# o 其他用户权限
# a 所有用户(文件属主,同组用户及其他用户)
# operator的含义:
# + 增加权限
# - 取消权限
# = 设定权限
# permission的含义:
# r 读权限
# w 写权限
# x 执行权限
# s 文件属主和组set-ID
# t 粘性位*
# l 给文件加锁,使其他用户无法访问
# u,g,o 针对文件属主,同族用户及其他用户的操作
```

![赋权操作](imgs\赋权操作.png)

### 绝对命令

![八进制目录文件权限表](imgs\八进制目录文件权限表.png)

### chmod命令的其他例子

![chmod命令](imgs\chmod命令.png)

## 目录

![目录权限](imgs\目录权限.png)

如果把同组用户或其他用户针对某一目录的权限设置为(--x)1 那么他们将无法列出该目录中的文件.如果该目录中由一个执行位置位的脚本或程序,只要用户知道他的路径和文件名,仍然可以执行他,用户不能静茹该目录并妨碍他的执行

## suid/guid

