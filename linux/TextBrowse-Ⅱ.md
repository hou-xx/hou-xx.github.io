## linux 系统文本浏览 Ⅱ
### cat 命令
> 将文件全部内容打印到标准输出流中，适用于文件内容较小的情况（常用 -n -b行首打印行数 ）

命令格式：
```
cat [-AbeEnstTuv] [--help] [--version] fileName
```
参数详解：       

参数|含义
:---:|:---:
-n / --number | 输出行号（对输出内容从 1 编号）
-b / --number-nonblank | 空白行不编号，其余同 -n
-s / --squeeze-blank | 当遇到有连续两行以上空白行，<br/>代换为一行空白行
-v / --show-nonprinting | 显示非打印字符<br/>（以 ^ 和 M- 符号显示，LFD 和 TAB 除外）
-E / --show-ends | 在每行结束处显示 $
-T / --show-tabs | 将 TAB 字符显示为 ^I
-A / --show-all | 等价于 -vET
-e | 等价于"-vE"
-t | 等价于"-vT"

示例：
```
// 把 t1 t2 合并的内容加上行号后追加到t3
cat -n t1 t2 >> t3
```

### more 命令
> more 的作用 cat 相似，它会 **加载全部文件内容**，以一页一页的形式显示，方便使用者逐页阅读，可以在浏览中使用搜寻字符串等功能。            
> 但是 **less is more**。 less 更强大，完全覆盖 more。所以，more 直接被无情跳过。

### less 命令
> 对于用户来说，less 是 more 的升级加强版。           

- less 不会一次性读取整个文件，对于大文件更友好；
- 支持比 more 更多的浏览命令（如：more 只能向前，less 可以向前、向后）。

命令格式：
```
less [参数] fileName 
```
参数详解：       

参数|含义
:---:|:---:
+num | 从第 num 行开始显示
+/context | 在文档显示前搜寻字串（context），<br/>从该字串之后开始显示
-N | 显示行号
-o filename | 将输出内容保存到指定文件
-e | 文件显示结束后自动离开
-b 缓冲区大小 | 设置缓冲区的大小
-f | 强制打开
-g | 只标志最后搜索的关键词<br/>（只标志当前搜索到的关键字，可继续搜索）
-i | 搜索忽略大小写
-m | 显示百分比（类似 more）
-Q | 不使用警告音<br/>（超烦的错误警告，谁用谁知道）
-s | 显示连续空行为一行
-S | 行过长时间将超出部分舍弃

浏览命令详解：

命令|作用
:---:|:---:
/context | 向下搜索 context
?context | 向上搜索 context
&pattern  | 仅显示匹配 pattern 的行
n | 重复前一个搜索<br/>（方向取决于 / 或 ?）
N | 反向重复前一个搜索<br/>（方向取决于 / 或 ?）
空格键/z/[pagedown] | 向下滚动一页
d | 向下翻半页
回车键 | 向下滚动一行
b/[pageup] | 向上翻一页
u | 向上滚动半页
y | 向上滚动一行
Q/q | 退出 less
v | 进入 vi 模式编辑文件
h | 显示 less 帮助文档

示例：
```
// 显示行号、忽略大小写从第一个出现 dubbo 的行
// 开始输出文件内容并进入向下搜索 dubbo 模式
less +/dubbo -Ni stdout.log 
```




