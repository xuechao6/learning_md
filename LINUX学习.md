# LINUX学习

## cat命令

1. 显示文件内容：`cat file.txt`，将显示文件`file.txt`的内容。
2. 连接多个文件：`cat file1.txt file2.txt`，将连接文件`file1.txt`和`file2.txt`的内容，并按顺序显示。
3. 创建文件：`cat > file.txt`，在命令行中输入内容，并按Ctrl+D结束输入，将内容保存到`file.txt`文件中。
4. 追加内容到文件：`cat >> file.txt`，在命令行中输入内容，并按Ctrl+D结束输入，将内容追加到`file.txt`文件的末尾。
5. 复制文件：`cat source.txt > destination.txt`，将`source.txt`文件的内容复制到`destination.txt`文件中。
6. 显示行号：`cat -n file.txt`，显示文件`file.txt`的内容，并在每一行前面加上行号。
7. 显示非空行：`cat -s file.txt`，显示文件`file.txt`的内容，将连续的空行压缩成一行显示。



## shell脚本

学习网站：[Shell 教程 | 菜鸟教程 (runoob.com)](https://www.runoob.com/linux/linux-shell.html)

shell脚本对空格的要求非常严格

### 1.变量

**解释器**：第一行是解释器，类似规定这个脚本以什么方式运行`#!/bin/bash`

**引用**：变量引用需要加上`$变量`，或者是`${变量}`；还可以使用`$(hostname或ls catkin_ws等)`使用linux系统的命令引用参数

**赋值**：变量一般用大写子母表示；变量赋值一般等号左边和右边都不留空格，和编程不太一样

**删除变量**：`unset + 变量名称`注意变量名称前面不加`$`，只有具体数值传递时采用，删除后数据无法再使用

```bash
#!/bin/bash
echo "hello shell"
MY_NAME="XC" 
echo my name is ${MY_NAME}

cd catkin_ws
list=$(ls build)
echo $list

read -p "enter your name:" value_save
echo read test name is $value_save
unset value_save
echo "删除后的结果为：${value_save}"

declare -i my_int=42
# declare -A my_array
# my_array["name"]="xc"
# my_array["age"]=$my_int
echo "int类型的数据为：${my_int}"

echo ${PATH}
```

### 2.传递参数

获取参数的形式为`${n}`,**n** 代表一个数字，**1** 为执行脚本的第一个参数，**2** 为执行脚本的第二个参数

```bash
shell脚本命令
echo "shell传递参数"
echo "文件名称为${0}"
echo "第1个参数为${1}"
echo "第2个参数为${2}"
echo "第3个参数为${3}"

终端命令：./test_shell.sh 6 5 4
输出结果
shell传递参数
文件名称为./test_shell.sh
第1个参数为6
第2个参数为5
第3个参数为4
```

### 3.流程

**if函数**：

- 语法

- ```bash
  if condition          if condition
  then                  then
      command1               command1
      command2          elif 
      ...                    command1
  else                  else  
      command                command
  fi                    fi
  ```

- 判断语句格式：`[ ... ]`和`(( ... ))`，第一种的大于`-gt`，小于用`-lt`，第二种可以直接用运算符。需要注意的是无论哪一种，括号内部前和后，都需要用空格隔开，运算符也需要空格隔开

- ```bash
  a=10
  b=20
  if [ ${a} -lt ${b} ] #需要用空格隔开
  then
      echo "a小于b"
  else
      echo "a大于b"
  fi
  
  if (( ${a} < ${b} ))
  then
      echo "a小于b"
  else
      echo "a大于b"
  fi
  ```

  **for语句**

  - **语法：**in 列表可以包含替换、字符串和文件名。

  - ```bash
    for var in item1 item2 ... itemN
    do
        command1
        command2
        ...
        commandN
    done
    ```

  - 举例：`this is a string`例如这句话，如果不加双引号，就会按照单词依次输出，如果加上双引号，就是一个字符串，整句输出一次

  - ```bash
    for var in $(ls) #in后面的位置也可以换成1,2,3等，或者是字符串一句话，分别打印每个单词
    do
        echo $var
    done   
    ```

  **case语句**

  - **语法：**

  - 取值后面必须为单词 **in**，每一模式必须以右括号结束。取值可以为变量或常数，匹配发现取值符合某一模式后，其间所有命令开始执行直至 **;;**。

    取值将检测匹配的每一个模式。一旦模式匹配，则执行完匹配模式相应命令后不再继续其他模式。如果无一匹配模式，使用星号 ***** 捕获该值，再执行后面的命令。

    最后的结束语是case反过来

  - ```bash
    case 值 in
    模式1)
        command1
        command2
        ...
        commandN
        ;;
    模式2)
        command1
        command2
        ...
        commandN
        ;;
     *)
     	command
     	;;
    esac
    ```

    举例

  - ```bash
    echo '输入 1 到 4 之间的数字:'
    echo '你输入的数字为:'
    read aNum
    case $aNum in
        1)  echo '你选择了 1'
        ;;
        2)  echo '你选择了 2'
        ;;
        3)  echo '你选择了 3'
        ;;
        4)  echo '你选择了 4'
        ;;
        *)  echo '你没有输入 1 到 4 之间的数字'
        ;;
    esac
    ```

  


### 4.函数

  **返回值**：参数返回，可以显示加**return** 返回，如果不加，将以最后一条命令运行结果，作为返回值。 **return** 后跟数值 **n(0-255)**，用`$?`				用来接收函数的返回值，return只能返回数值，其他的不行

  **举例**：先写定义，和c++编程一样，但是在调用时不写括号

```bash
#!/bin/bash
funWithReturn(){
    echo "这个函数会对输入的两个数字进行相加运算..."
    echo "输入第一个数字: "
    read aNum
    echo "输入第二个数字: "
    read anotherNum
    echo "两个数字分别为 $aNum 和 $anotherNum !"
    return $(($aNum+$anotherNum))
}
funWithReturn
echo "输入的两个数字之和为 $? !"
```

**传参**：函数后面紧跟着参数，并不是编程的形参传递，传参可以是任何类型，但是返回值只能是0~255的int

```bash
funWithParam(){
    echo "第一个参数为 $1 !"
    echo "第二个参数为 $2 !"
    echo "第十个参数为 ${10} !"
    echo "第十一个参数为 ${11} !"
    echo "参数总数有 $# 个!"
    echo "作为一个字符串输出所有参数 $* !"
}
funWithParam 1 2 3 4 5 6 7 8 9 34 73
```



### 5.启动其他脚本

采用`.`的方式引入其他脚本，例如`. .test_shell02.sh`，注意两个点之间需要有空格



### 6.启动新的终端

`gnome-terminal -t " title-name " -x bash -c " sh ./test_shell02.sh; exec bash;"`

- `-t` 为打开终端的标题，便于区分
- `-x` 后面的为要在打开的终端中执行的脚本，根据需要自己修改就行了
- `exec bash;` 是让打开的终端在执行完脚本后不关闭
- ` bash -c` 执行shell命令