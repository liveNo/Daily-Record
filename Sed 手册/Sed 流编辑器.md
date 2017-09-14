# Sed 基础使用
> http://www.gnu.org/software/sed/manual/sed.html#Introduction


## 简介
`sed`是一款流编辑器。流编辑器用于对输出流或者文件进行基本的文本转换。在某些方面类似于允许脚本编辑的编辑器，但是`sed`只能运行一次编辑，因此效率也比较高。但是`sed`的过滤能力是其一大特色，这有区别与其他类型的编辑器，因此，玩`sed`的人，正则表达式的能力也相对较强。

## 命令参数选项

完整的调用`sed`格式是：

```
sed OPTIONS... [SCRIPT] [INPUTFILE...]
```

- --version 打印正在运行的`sed`的版本和版权，并退出
- --help 帮助文档，不解释
- n, --quiet, --silent  屏蔽匹配模式的自动打印
- -e script, --expression=script 增加要执行的脚本命令
- -f script-file, --file=script-file  增加要要执行的脚本文件
- -i[SUFFIX], --in-place[=SUFFIX]  替换并修改文件处理后的内容
- -l N, --line-length=N  为`l`命令指定所需的行包长度
- -s, --separate  把文件看作是独立的，而不是单一的连续的长流。
- -r, --regexp-extended  在脚本中使用扩展的正则表达式

## 概览

### 常规使用

`sed` 调用模式

```
sed SCRIPT INPUTFILE...
```

`s`命令替换，例如，将input.txt中的`hello`全部转换成`world`

```
sed 's/hello/world/' input.txt > output.txt
```

其中，`s`表示替换命令。以上命令只会匹配替换每行的第一个`hello`，而如果想要替换一行上所有匹配则需要使用`g`，如下

```
sed 's/hello/world/g' input.txt > output.txt
```

**注意1** 我们在替换字符串中时经常会用到单引号（'）或者双引号（"），如果在使用`sed`命令过程中，你要使用单引号，那么在引号之间是没有办法通过`\`来转义的，我们直接使用双引号取可。但是在双引号之间是可以通过`\`来转义的。

**注意2** 以上的`sed`命令并不会改变源文件，只是把修改后的内容输出或者重写向到`output.txt`文件中。

`-i` 直接修改文件内容，如下

```
sed -i "s/hello/world/g" input.txt
```

`sed`默认打印全部处理过的输入，可以使用`-n`来屏蔽输出，`p`命令可以打印具体的行，如下命令只会打印出第5行:

```
sed -n "5p" input.txt
```

`sed`也能同时处理多个文件，但是会将多个文件视为一个长流，如下会打印出第一行（input.txt）与最后一行（output.txt）

```
sed -n '2p ; $p' input.txt output.txt
```

只替换第N行某个词，即替换指定内容

```
sed '3s/hello/world/g' input.txt
```
如果要替换第5到8行的某个词，则如下：

```
sed '5,8s/hello/world/g' input.txt
```

以上是横向限制，如果是纵向限制，如只替换每行匹配到的第一个，如下

```
sed 's/hello/world/1' input.txt  # 只替换每一行的第一个hello
sed 's/hello/world/2' input.txt  # 只替换每一行的第二个hello
sed 's/hello/world/4g' input.txt # 只替换第一行的第四个以后所有的hello
```

多个匹配，每个命令可以使用分号分隔，如下

```
sed 's/hello/world/2 ; s/hello/hi/3g' input.txt 
```
而同上面这个命令类似使用`-e`参数，如下

```
sed -e 's/hello/world/2' -e 's/hello/hi/3g' input.txt
```


### 正则匹配

在每行最前面加上`#`

```
sed "s/^/#/" input.txt
```
在每行最后面加上`~`

```
sed "s/$/~/" input.txt
```

**正则表达式的特殊字符含义**

- ^ 表示一行的开头。如：/^#/ 以#开头的匹配。
- $ 表示一行的结尾。如：/}$/ 以}结尾的匹配。
- \< 表示词首。 如：\<abc 表示以 abc 为首的詞。
- \> 表示词尾。 如：abc\> 表示以 abc 結尾的詞。
- . 表示任何单个字符。
- * 表示某个字符出现了0次或多次。
- [ ] 字符集合。 如：[abc] 表示匹配a或b或c，还有 [a-zA-Z] 表示匹配所有的26个字符。如果其中有^表示反，如 [^a] 表示非a的字符

例如，现在有一h.html代码如下：

```
<b>I</b> am <i>Joey</i>, And I like <span style="text-decoration: underline;">basketball</span> Haha.
```
如果使用以下匹配

```
sed "s/<.*>//g" h.html
## 结果为 haha
```
显然不是我们想要的结果。如果想要解决以上问题的话，其中的'[^>]' 指定了除了>的字符重复0次或多次，具体如下：

```
sed "s/<[^>]*>//g" h.html  # 结果：I am Joey, And I like basketball haha
```