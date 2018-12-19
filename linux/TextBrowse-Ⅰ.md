## linux 系统文本浏览Ⅰ
### 基础概念
#### 流
> 不管是来自文件、键盘、显示器或其他的I/O设备，Linux shell 接收或发送都是字符串流形式的标准输入和输出         

流分为三种：      

- stdin ，标准输入，文件描述符是 0      
- stdout ，标准输出，文件描述符是 1       
- stderr ，错误输出，文件描述符是 2       
#### 重定向
> 输入/输出流可以重定向到文件或其他设备。包含输入重定向操作符 `<` 、输出重定向操作符 `>`。     

常用操作符           

操作符语法|含义
:---:|:---:
command > file | 将命令输出重定向到 file （stdout）
command < file | 将输入重定向到 file<br/>（以file内容为命令输入）
command >> file | 将命令输出以 **追加** 方式<br/>重定向到 file （stdout）
command n > file | 将命令输出中文件描述符为 n 的内容<br/>重定向到 file
command > file n >& m | 将命令输出中文件描述符为 n 和 m 的内容合并<br/>重定向到 file
<< tag | 将开始标记和结束标记之间的内容作为输入<br/>（tag 只是标记名，可自定义）

示例： 
```
//所有重定向到文件，文件不存在都会自动创建

//将 ls 的结果输入到 test.txt 文件（覆盖方式）
ls > test.txt

//将 ls 的结果输入到 test.txt 文件（追加方式）
ls >> test.txt

//将 sh deploy.sh 的标准输出追加到 text.txt
sh deploy.sh >> test.txt

//将 sh deploy.sh 的标准输出和错误输出合并追加到 text.txt
sh deploy.sh >> test.txt 2>&1

// 计算 test.txt 行数（test.txt 内容作为输入）
wc -l < test.txt

// command < file1 >file2
// 计算 test.txt 行数写入 lines.txt
wc -l < test.txt > lines.txt

```
**PS:** /dev/null 是一个特殊的文件，写入到它的内容都会被丢弃。        
`command > /dev/null 2>&1` 即屏蔽 command 所有的输出

#### 管道
> 管道是一个位于内存中的文件系统缓冲区(没有中间文件)，作用是将的一个命令的 stdout 指向第二个命令的 stdin；      
> 操作符是 `|`          
> 结合xargs命令，可以将输出重定向为命令参数（以后会常见）    

命令格式：
```
// c 指 command、p 指 parameter
c1 p1 | c2 p2 | c3 ps p4 | c4|····
```
