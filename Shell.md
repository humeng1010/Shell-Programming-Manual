# Shell

## Shell如何执行？

> Shell的执行方式有很多中，那这几种有什么区别呢？下面我将介绍如何执行Shell脚本以及使用该方法执行脚本的区别

前提：准备一个`hello.sh`的文件并写入输出语句

```sh
#!/bin/bash
echo "hello,world"
```

执行方式：

1. `sh [相对路径|绝对路径]`或者`bash [相对路径|绝对路径]`

   > 注意：使用该方式执行脚本是在当前(父)shell(即当前的环境命令行，命令行也是一个shell的执行环境)中打开了一个子shell来执行脚本内容，当脚本执行完毕后，子shell关闭，回到父shell中。不会影响到父shell的环境变量

2. 给脚本文件可执行的权限后直接通过相对路径或绝对路径执行脚本

   ```sh
   # 给hello.sh权限
   chmod 755 hello.sh
   ```

   > 关于这条命令可以在[一周学会Linux中查看](http://wuluwulu.cn/archives/linuxweek#chmod)是什么意思

   ```sh
   # 在当前目录下
   ./hello.sh
   # 或者使用绝对路径执行
   ```

   > 区别和上方的一样：使用该方式执行脚本是在当前(父)shell(即当前的环境命令行，命令行也是一个shell的执行环境)中打开了一个子shell来执行脚本内容，当脚本执行完毕后，子shell关闭，回到父shell中。不会影响到父shell的环境变量

3. `source hello.sh`或`. hello.sh`

   > 注意：使用该方式执行shell脚本，不需要打开子shell，**直接在当前的shell环境中执行脚本**。所以可以影响当前环境的环境变量。
   >
   > 例如：我们在配置环境变量的时候当我们修改完`/etc/profile`文件后，需要执行`source /etc/profile`命令使配置立即生效。
   >
   > 注意📢：`. hello.sh`前面的`.`是一条命令

___关于父子Shell这里做出一个详细的介绍：___

> 我们使用`ps -f`查看当前用户的进程
>
> ![image-20230405165435459](https://blog-images-1309758663.cos.ap-nanjing.myqcloud.com/202304051654606.png)
>
> 我们执行`bash`命令，然后再查看当前用户进程
>
> ![image-20230405165756318](https://blog-images-1309758663.cos.ap-nanjing.myqcloud.com/202304051657404.png)
>
> 我们知道在正常情况下执行`exit`命令会直接退出虚拟机连接，那我们现在执行`exit`会发生什么呢?
>
> ![image-20230405170004911](https://blog-images-1309758663.cos.ap-nanjing.myqcloud.com/202304051700973.png)
>
> **这就是父子shell嵌套：在父shell中使用bash或sh命令会进入子shell中，具体的区别就是：子shell中设置的当前变量父shell是不可见的！**

接下来我们来学习什么是变量



## 变量

### 环境变量

#### 常用的系统变量

- $HOME
- $PWD
- $SHELL
- $USER
- ······

> 可以使用`echo $HOME`查看环境变量(当前用户的home目录)
>
> 使用`env`(只有系统的环境变量)或者`set`(有当前shell所有的环境变量)查看当前shell所有环境变量

#### 关于全局变量和局部变量的说明

全局变量在所有的shell中都可以查看不管嵌套多少层。

而局部变量只可以在当前的shell中查看，在其他的shell中均不可以查看



### 自定义变量

```sh
a=2 # 定义变量 
echo $a # 输出变量 $ 符号通常用于获取变量
echo $hello # 如果输出没有定义的变量，输出内容是空
hello=hello
echo $hello
hello=HELLO # 可以直接修改变量
echo $hello
hello="HELLO WORLD" # 如果变量的值中存在特殊的符号建议使用引号(单引号也可以)
echo $hello
hello='HELLO WORLD'
echo $hello
```

> 使用`env | grep hello`查看当前的hello是属于全局环境变量还是局部：结果是局部的。那我们想把该变量导出为全局的变量该怎么做呢？
>
> 我们可以使用`export hello`把hello变量导出为全局环境变量。
>
> 注意全局环境变量在子shell中进行修改不会影响到父shell。(即使在子shell中再次export也不会影响到父shell)

```sh
readonly b=5 #只读变量——常量(不能修改也不能撤销)

unset a # 撤销a变量
```

> 1. 变量名称可以由字母数字下划线组成，但是不能以数字开头，**环境变量的名称建议大写**
> 1. 等号两侧不能有空格
> 1. 在bash中，变量默认类型为字符串

### 特殊变量

#### $n

> $n：n为数字，$0代表该脚本的名称，$1-$9代表第一到第九个参数，十以上的参数需要使用大括号包裹起来：${10}

```sh
cat test2
# shell脚本内容
echo '=======test $n========'  # 使用单引号引起来的内容会作为原样直接输出!!!!!!!!!!
echo "commend name $0"
echo "1 paramter $1"
echo "2 paramter $2"
echo "3 paramter $3"

# 执行该脚本并跟上参数
./test2 "com.mysql.cj.jdbc.Driver" "jdbc:mysql://localhost:3306/halo" "root"
# 输出内容
=======test $n========
commend name ./test2
1 paramter com.mysql.cj.jdbc.Driver
2 paramter jdbc:mysql://localhost:3306/halo
3 paramter root
```

#### $#

> <span style="color:red">获取所有输入参数的个数(不包含脚本，只统计参数)</span>(通常用于循环，判断参数个数是否正确以及加强脚本的健壮性)

```sh
cat test3
#!/bin/bash
echo '=======$#========='
echo script name: $0
echo 1st parameter: $1
echo 2nd parameter: $2
echo parameter total: $#
echo '=================='


./test3 java go vue js spring
=======$#=========
script name: ./test3
1st parameter: java
2nd parameter: go
parameter total: 5
==================
```

#### $*和$@

> $*  这个变量代表命令行中所有的参数，$*把所有的参数看成一个整体
>
> $@ 这个也代表命令行中的所有参数，不过$@把每个参数区分对待

#### $?

> 得到最后一次的命令的返回状态。如果是0代表上一个命令执行正确



## 运算符

> 在Shell里面需要运算和其他的语言有很大的区别

语法：$[1+1]或者$((1+1))

示例：

> 编写一个add命令并且后面可以跟上被加数和加数得到结果的程序

```sh
echo $[$1+$2]
```

