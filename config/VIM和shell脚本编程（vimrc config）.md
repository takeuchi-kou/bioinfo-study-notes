视频教程：https://www.bilibili.com/video/BV1H7411s7xH?p=2&spm_id_from=pageDriver

[TOC]

# 1. 配置vimrc

工欲善其事，必先利其器。为了让编写shell脚本更容易，用户可以自己配置vimrc，从而实现语法高亮、error提示以及增加其他插件功能。详细可以参考这个博客《手把手教你把Vim改装成一个IDE编程环境》：https://blog.csdn.net/wooin/article/details/1858917

我想要vim实现的主要功能有：

1. 语法高亮
2. 语法错误即时提示

## 1.1 语法高亮

首先配置.vimrc，输入
```
syntax on
```
以开启语法高亮。
选择molokai配色方案。
将molokai.vim文件下载到`~/.vim/colors`文件夹下（没有则创建）
在.vimrc文件中添加

```
colorscheme molokai
```
即可


## 1.2 语法错误检查工具
使用插件：Syntastic  
git地址：https://github.com/vim-syntastic/syntastic  
需要先安装pathogen（vim插件工具）  
中文版安装指引可参考：https://blog.csdn.net/lpb2019/article/details/102757318  

## 1.3 .vimrc配置概览
```
" get rid of compatible mode to avoid bugs"
set nocompatible
" show the line number"
set number
" use evening mode for background"
color evening
" highlight the syntax"
syntax on
" set color scheme"
colorscheme  molokai
set t_Co=256
set background=dark
" highlight search results"
set hlsearch
" searching when input"
set incsearch
" indent automaticlly when open a new line"
set smartindent
" complete the brackets"
set showmatch
" syntastic config
execute pathogen#infect()
syntax on
set background=dark
" highlight search results"
set hlsearch
" searching when input"
set incsearch
" indent automaticlly when open a new line"
set smartindent
" complete the brackets"
set showmatch
" syntastic config
execute pathogen#infect()
syntax on
filetype plugin indent on

set statusline+=%#warningmsg#
set statusline+=%{SyntasticStatuslineFlag()}
set statusline+=%*

let g:syntastic_always_populate_loc_list = 1
let g:syntastic_auto_loc_list = 1
let g:syntastic_check_on_open = 1
let g:syntastic_check_on_wq = 0
```




# 2变量

## 2.1 声明脚本

```
#！/usr/bin/env bash
```
`#!`声明自己是一个脚本文件，后面表示脚本使用的解释器以及存在的位置。
在首行加了声明之后，在terminal输入

```
chmod u+x xx.sh #使文件可执行
```
就可以使用`./xx.sh`命令执行脚本了。

没有加声明的话需要用`bash`命令执行.

## 2.2 变量

```bash
#!/usr/bin/env bash

PRICE_PER_APPLE=5
greeting='Hello         world!'
echo "The price of an Apple today is: \$HK $PRICE_PER_APPLE"
echo 'The price of an Apple today is: \$HK $PRICE_PER_APPLE'
```

1. **变量名区分大小写**  

2. **=左右两边不能有空格**

3. **单引号**包围的字符串中**不对**特殊符号做解释执行*(引号里啥样打出来就是啥样)*  ；双引号对特殊符号进行解释执行

4. 使用**\转义符号**避免被解释执行。

   例如上面的代码中单双引号输出区别如下。

![image-20211108095319926](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211108095319926.png)

5. 使用**${}包围变量名**避免变量名被解释执行时的二义性。

   例如

```bash
MyFirstLetters=ABC
echo "The first 10 letters in the alphabet are:
${MyFirstLetters}DEFGHIJ"
```

如果不加{}那么系统会认为后边一串都是变量，出现报错。

6. 使用**双引号**包围变量名可以保留所有空格字符,否则默认只输出一个空格。

   例如
   
   ```bash
   greeting='Hello         world!'
   echo $greeting "now with spaces: $greeting"
   ```
   
   得到：
   
   ```
   Hello world! now with spaces: Hello         world!
   ```
   
   可以看到双引号外边的变量中的空格被压缩成1个。

7. 其他程序的输出结果通过**反引号``**或者**$()**直接赋值给shell变量

   ```
   FILELIST=`ls`
   FileWithTimeStamp=/tmp/file_$(/bin/date +%Y-%m-%d).txt
   ```

   得到

   ```
   1.sh 2.sh
   /tmp/file_2021-11-08.txt
   ```

   

# 3. 调试脚本

### 方法1

在terminal中执行脚本时，用 `-x`使用调试模式：

```
bash -x 1.sh
```

得到结果：

```
+ greeting='Hello         world!'
+ echo Hello 'world!' 'now with spaces: Hello         world!'
Hello world! now with spaces: Hello         world!
```

其中以+为首的行代表脚本内容，不带+的行是输出内容

### 方法2

临时修改脚本，在脚本中需要调试的段落前后各加上开始和结束调试的命令行：

```
set -x  	# activate debugging
content needs debug
set +x  	# stop debugging
```

# 4. 给脚本传参

## 4.1 传参规则：

- 参照使用C语言传参语法规范
- 参数与参数之间、脚本文件名与参数之间使用1个或多个空格分隔
- $0指代脚本文件本身
- $1指代命令行上的第1个参数，$2指代命令行上的第2个参数，以此类推
- $@指代命令行上所有参数（参数数组）
- $#指代命令行上的参数个数（参数数组大小）

## 4.2 示例

脚本内容：

```
echo $3
BIG=$5
echo "A $BIG costs just $6"
echo "$@"
echo $#
```

用调试模式执行得到：

![image-20211108192145731](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211108192145731.png)

所有参数位置都为空（参数个数为0）

在执行脚本时传递参数：

```
bash -x test.sh apple 5 banana 8 "Fruit Basket" 15
```

结果

![image-20211108192533438](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211108192533438.png)

# 5. 数组

### 规则：

- declare -a 声明的是索引数组（数字下标）,默认为索引数组；

- declare -A声明的是关联数组（字符串下标）；

- 如果同时使用-a -A，-A优先级更高，声明为关联数组。

### 遍历数组的方法

```bash
# 遍历索引数组
for i in "${array[@]}“;do
	echo "$i"
done

# 关联数组
for key in "${!associative_arr[@]}";do
	echo "${associative_arr[$key]}"
done
```



# 6. 基本算数运算

1. 使用`$((expression))`算术运算符表达式，注意这种方式只支持**整数**运算。 

2. 进阶算数可以使用命令行工具bc，`-l`表示使用标准数学库

   ```
   # 计算4*arctangent(1)，保留十位有效数字
   pi=$(echo "scale=10; 4*a(1)" | bc -l)
   ```

   

# 7. 字符串操作

## 7.1 获取中/英字符串长度

```
# 获得字符串长度值
STRING="this is a string"
echo ${#STRING}    #16

# 中文字符长度获取
M_STRING="中文"
export LANG=C.UTF-8
echo ${#M_STRING}       #字符长度 2
export LANG=C
echo ${#M_STRING}    #字节长度 6
```

## 7.2 字符串截取子串

运算符为：`${string:start_pos:substr_len}`  

注意这里是从0开始计数

```
STRING="this is a string"
POS=1
LEN=3
echo ${STRING:$POS:$LEN}  #his 第2到第4个字符
echo ${STRING:1}	#his is a string  从第2字符开始
```

### 其他方法

```
echo $STRING | cut -c 1-8		#截取1-8字符
echo $STRING | awk '{print substr($0,1,8)}'		#截取1-8字符
```



## 7.3 字符串的查找替换

### 方法 1 shell命令

```
STRING="to be or not to be"

# 查找并替换所有匹配到的子集
echo ${STRING[@]//be/eat}

# 查找并删除（替换为空）所有匹配到的子集
echo ${STRING[@]// NOT/}

# 查找并替换匹配到行首的子串
echo ${STRING[@]/#to be/eat now}

# 查找并替换匹配到行尾的子串
echo ${STRING[@]/%be/eat}

# 查找替换并使用子命令输出结果替换匹配项
echo ${STRING[@]/%be/be on $(date +%Y-%m-%d)}
```



### 方法2 sed命令

```
sed 's/Search_String/Replacement_String/g' Input_File
```

注意sed命令是对文件而非单个字符串进行操作，并且打印结果到标准输出。  

若需修改文件则使用选项`-i`

# 8. 条件判断

- 逻辑块规则：

```
if [[ expression ]]; then
# code if expression is true
elif [[ expression ]]; then
# code if expression is true
else
# other condition
fi
```

- 逻辑运算符包括： 

  `！`取反  

  `&&`与  

  `||`或  

- 使用逻辑运算符构成的条件表达式应使用`[[ ]]`包围而非[]



# 9. 循环

## 9.1 for循环

```
# basic construct
for arg in [list]
do
	command(s)...
done

# 单行结构
for arg in [list]:do command(s)...;done
```

- 实例 1：for循环遍历一个提前定义的列表

  ```
  # loop on array member
  NAMES=(Joe Jenny Sara Tony)
  for N in ${NAMES[@]} ; do
  	echo "My name is $N"
  done
  ```

  结果：

  ```
  My name is Joe
  My name is Jenny
  My name is Sara
  My name is Tony
  ```

  

- 实例 2：for循环遍历一个外部指令输出结果 。  

  注意这里的**外部命令输出结果作为变量被引用时要用圆括号`$(output)`**。

  ```
  # loop on command output results
  IFS=$'\n'
  for f in $(ps -eo command) ; do
  	echo "$f"
  done
  ```

  `IFS：`The Internal Field Separator that is used for word splitting after  expansion  and  to  split lines into words with the read builtin command.The default value is ``<space><tab><newline>''.循环输出结果中含有空格，同一行会被分隔为不同字段。将IFS指定为换行符，可以避免这种情况。

  结果是被分隔成不同行输出的进程

  ![image-20211109194141952](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211109194141952.png)

## 9.2 while循环

- 基本循环

```
# basic construct
while [ condition ]
do
	command(s)...
done 
```

- do...while循环

  ```
  while [ : ]; do
  	if [ condition ]; then
  		break
  	fi
  	command(s)...
  done
  ```

- 循环控制

  `break` 跳过本层循环体剩余部分代码并返回上一级代码片段  

  `continue`跳过本次循环剩余代码，继续执行下一次循环条件表达式计算判断。

- 实例

```
COUNT=4
while [ $COUNT -gt 0 ]; do
	echo "Value of count is: $COUNT"
	COUNT=$(($COUNT - 1))
done

while [ : ]; do
	echo "Value of count is: $COUNT"
	COUNT=$(($COUNT - 1))
	if [[ $COUNT -eq 0 ]]; then
		break
	fi
done
```

结果：

```
Value of count is: 4
Value of count is: 3
Value of count is: 2
Value of count is: 1
Value of count is: 4
Value of count is: 3
Value of count is: 2
Value of count is: 1
```

