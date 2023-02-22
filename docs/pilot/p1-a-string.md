# 字符与字符串

在我们进入这一部分最后的关卡，面对关底 Boss 之前，我们需要稍微做一点准备，补上关于 Python 字符和字符串的一些知识。

字符串可能是编程中使用最多的基本数据类型，Python 提供了强大的字符与字符串处理能力，我们经常用到的包括：
* 一个名为 `string` 的模块（*module*），提供了常用的一些字符串（比如所有英文大写和小写字母）；
* 一个名为 `str` 的类，Python 里的字符串就是这个类的对象实例；
* 一个名为 `re` 的模块，提供对**正则表达式**（*regular expression*）的支持，这是一个强大的字符串匹配查找和替换工具，我们以后会介绍。

这一章我们就来学习上面列出的前两个。

## 字符、ASCII 和 Unicode

字符（*character*）我们人类文字的最小单元，字母、数字、标点和其他符号都是字符，中日韩的单字也是字符——中日韩语言的独特性是老外发明计算机和各种初期标准时没太多考虑的，然后一直痛到现在。

开始时还是挺简单的，字符在计算机里其实就是用整数表示的，一个整数对应一个字符，查表就知道哪个是哪个，这个表叫做 [ASCII](https://www.cs.cmu.edu/~pattis/15-1XX/common/handouts/ascii.html)（全称是 *American Standard Code for Information Interchange*），里面有不多不少 128 个字符，用 7 位二进制数（即 7 个比特——不是 7 个比特币）或者两位十六进制数就可以搞定，还空出了最高的一位（为 0）。

ASCII 码完美解决了英语问题，在计算机发展的初级阶段英语是标准语言也是唯一可用的语言。后来非英语的欧美国家通过扩展 ASCII 码表引入了希腊语、俄语等语种字符，方法就是把一个字节的另外 128 个数用上，扩展出来的那些字符编码的最高位固定为 1，这样保持和 ASCII 兼容，称为 EASCII 或者 High ASCII。

真正颠覆性的变化是中日计算机领域的发展。中国仅收入《新华字典》的常用汉字就有 10000 多个，日本也有超过 2000 个常用汉字和约 100 个假名字符，远远超出 ASCII 或者一个字节能表示的范围，于是中日（还有韩国和中国港澳台地区）纷纷动手制定自己的字符集和编码标准。

很多人不知道，我国在 1980 年就有了第一个计算机字符编码标准 GB2312-1980 公布实施，这个标准影响力之大，直到今天仍然存在，也让汉字编码（以及输入）的问题在计算机普及之前基本上就圆满解决了，真的要感谢中国最早一代的信息化专家们。

GB2312 包含 6000 多个常用汉字，还有 600 多包含日文假名在内的其他语言字符。GB2312 是双字节方案，用两个字节（16 个比特）表示一个字符，两个字节的最高位都是 1，这样不会和 ASCII 字符冲突，但会和 EASCII 字符冲突，所以如果不告诉计算机这是 GB2312 编码的文字，计算机会把一个汉字认成两个 EASCII 字符，显示一堆 Çéäæûá 给你看，这就是俗称的“乱码”。

整个 8、90 年代东亚地区的计算机都处于这样的窘境，不仅和欧美不兼容，彼此之间也不兼容，我国就有大陆的 GB2312 和港澳台地区的 Big5 两种互不兼容的编码体系，日本和韩国还有各自几个不一样的编码，那个年代的硬核玩家必然会装着动态切换文字编码的工具。

1991 年回过神来的行业领袖和国际标准组织终于开始着手统一工作，通过来自施乐（Xerox）和苹果（Apple）的专家和 ISO 组织一起推动，拿出了一个统一的字符集方案 UCS（Universal Coded Character Set），经过几十年的迭代，目前最新的 12.1 版本收录了超过 13 万个字符，涵盖了 150 多种现存和曾经存在的人类语言，还有好多空位，所以大家正在往里头使劲塞 Emoji 表情包。UCS 里每个字符都对应一个序号，这个序号采用四个字节表示，称为 UCS-4 编码。

这里要提醒大家，字符集和字符编码是两个不同的概念。字符集决定了可以被识别和处理的字符是哪些，而字符编码是计算机程序如何用一个数字来表示特定字符。就好像学校里就这么些学生，是固定的，但可以通过身份证也可以通过学号找到一个学生，那么身份证号和学号就是学生两种不同的编码方式。

UCS 中最常用的字符都集中在靠前部分，也就是 UCS-4 编码中只用到低位两字节而高位两字节都是 0 的那些，这部分字符又被称为“基本多文本平面（*Basic Multilingual Plane, BMP*）”，其实用两字节就可以编码，这个两字节编码方式就叫 UCS-2。

UCS 字符集和配套的 UCS-4、UCS-2 都是 ISO 国际标准，但大家很少直接用这两个编码，原因是：
1. UCS-4 编码要用四个字节，对于最经常使用的英文来说，ASCII 编码只需要一个字节，UCS-4 用四个字节而前面大部分都是 0，很浪费；即使中文，常用的一般也是两字节编码，用 UCS-4 也很浪费。
2. UCS-2 编码和东亚各国已经用了几十年的双字节编码不兼容，而且容量也不太够。

所以大家开始寻找变通方案，那就是把字符集和编码方式分开，字符集最大限度和 UCS 一致，但用不一样的办法来编码不同的字符。

我国颁布于 1993 年的 GB13000 就是字符集标准，和 UCS-2 兼容；而 2005 年颁布的 GB18030 则是编码标准，这个标准采用单、双和四字节的变长编码方案，单字节兼容 ASCII 码，双字节兼容 GB2312-1980 和 GBK 汉字编码，而四字节部分处理不太常用的汉字和少数民族文字。GB18030 是强制标准，所有在中国出售的软件系统和服务都必须支持。

另一个我们常用的编码方式是 UTF-8，同样采用变长方案，英文采用单字节编码，与 ASCII 兼容，西欧其他语言的字符采用双字节编码，常用汉字采用三字节编码，罕用字符采用四字节编码。UTF-8 对中文用户来说，汉字编码比 GB18030 多用一个字节，体积要增加 50%，不过仍然是支持最广泛的事实标准。

很多现代编辑器都提供转码功能，可以在这些主流编码之间方便的转换。从最大限度兼容的角度，一般都会使用 UTF-8 编码，无论是源代码还是文档。

在软件开发领域，不少编程语言和核心库都诞生在 Unicode 之前，原本都不支持 Unicode，后续陆陆续续添加了 Unicode 支持，到现在基本上都把 Unicode 作为缺省方案了。最近 20 年诞生的新编程语言则基本上从开始就建立在 Unicode 基础上，有些甚至允许你用 Emoji 表情做变量名——当然这不能算是个好的编码风格。

字符在计算机里就是用整数来表示的，英文字符毫无例外都是最古老的 ASCII 码，其他字符对应的整数看采用的编码格式。而 Python 的 `str` 类缺省采用 Unicode 编码格式。

Python 提供两个函数 `ord()` 和 `chr()` 来做字符和它对应的整数值之间的转换。前者给出输入字符对应的整数（UCS 编码的十进制表示），后者则反过来，根据输入的整数返回对应字符。


```python
ord('码')
```




    30721




```python
chr(30721)
```




    '码'




```python
chr(30722)
```




    '砂'




```python
c = '\u7802' # \u 表示后面是16位 UCS-2 编码，这里十六进制数 0x7802 = 30722
print(c)
```

    砂
    

Python 官网有一篇介绍 [Python 中 Unicode 支持](https://docs.python.org/3/howto/unicode.html) 的文章，可以作为延伸阅读材料。

## 字符串值

字符串基本上就是一个字符列表，在很多编程语言里就是按照字符列表（数组）来处理的，也允许程序员像操作数组一样操作字符串，不过因为字符串使用的实在是太频繁了（远比数字要多），对程序的基本性能有重大影响，所以大多数编程语言都会对字符串做些内部的特殊处理以提升性能，绝大部分情况下这些特殊处理对我们写程序来说是透明的，我们不需要去关注，只把字符串当做字符列表就可以了。

Python 里字符串值（*string literal*）可以用单引号、双引号、三个单引号或三个双引号来表示，主要区别是：
* 单引号里面可以包含双引号字符；
* 双引号里面可以包含单引号字符；
* 三个引号括起来的可以包含多行文字。


```python
'用单引号括起来的字符串里不能直接包含单引号，但可以有"双引号"'
```




    '用单引号括起来的字符串里不能直接包含单引号，但可以有"双引号"'




```python
"用双引号括起来的字符串里不能直接包含双引号，但可以有'单引号'"
```




    "用双引号括起来的字符串里不能直接包含双引号，但可以有'单引号'"




```python
"中文的引号其实是另外的字符，总是可以包含的”“‘’"
```




    '中文的引号其实是另外的字符，总是可以包含的”“‘’'




```python
'''三个单或者双引号可以包含多行文字
就像这样'''
```




    '三个单或者双引号可以包含多行文字\n就像这样'




```python
'其实多行文字并没有什么特别\n像这样插入一个换行字符也能实现'
```




    '其实多行文字并没有什么特别\n像这样插入一个换行字符也能实现'



上面的代码里我们看到了一个奇怪的东西 `\n`，关于这个有几点知识：

* 字符串值里反斜线 `\` 加一个字符这种东西叫“转义符（*escape character*）”，代表一个特殊字符或者特殊指令（告诉解释器后面的字符如何理解），Python 解释器会在处理时自动翻译或处理；Python 支持的转义符列表可以参考 [这里](http://python-ds.com/python-3-escape-sequences)，所以不管我们用单引号还是双引号都可以在字符值里用 `\"` `\'` 来输入需要的引号；如果我们想输入 `\` 怎么办？聪明人立刻就能想到，用 `\\` 就可以了；
* 转义符 `\n` 代表一个特殊字符，也就是 ASCII 里的换行（*LF*, *linefeed*）符；ASCII 里还有个回车（*CR*, *carriage return*）符，可以用 `\r` 表示；回车换行的概念来自老式打字机，打字机打完一行需要按两个功能键，一个是回车，即移动滑轨让打字位置回到行首，另一个是换行，即走纸一行让打字位置走到下一行空白处；在计算机时代我们敲击键盘的换行（↩︎, Return）键就能完成这两个工作；
* 如果在字符串前面写一个小写的 `r`，意思是 *raw*，就是告诉 Python 解释器这个字符串不要做任何转义处理，里面所有的 `\` 就是 `\` 字符，没有特殊含义，这种特殊标记的字符串有时候会有用，比如处理文件路径，或者正则表达式，后面我们会遇到的。

关于回车换行（LF 和 CR），有另外一个坑需要说下。当我们打开一个文本文件，无论是源代码还是别的什么，里面都有很多的回车换行符，藏在每一行行尾，并不肉眼可见，编辑器看到这些字符就另起一行显示内容，如果把这些回车换行符都去掉，整个文件就会显示成长长长长长的一行，那就没法读了。

然而，因为历史原因，不同的操作系统里保存文本文件时用的回车换行符是不一样的：
* Mac OS 9 以及更早的 Mac OS 用的是单回车，也就是 `\r`；
* Unix、Linux、Mac OS X 及改名的 macOS 用的是单换行，也就是 `\n`；
* Windows 用的是回车加换行，也就是 `\r\n`。

所以 Windows 下创建的文件拿到其他系统一般是没问题的，但是 Windows 打开其他系统创建的文件就可能识别不出新行而显示成一大串。不过好在这么多年了，这个坑已经为人所熟知，大部分编辑器都能很智能的识别，就算不能识别也会允许你设置好告诉它如何处理。

在 Visual Studio Code 中打开文件，右下角就会显示这个文件里使用的换行符是哪一种，点击可以修改：

![](assets/crlf-in-vsc.png)

## 组装字符串：f-string

我们写程序的时候经常要干的一件事是动态的攒一个字符串出来，里面有固定的文字，但是特定位置上要出现某些我们算出来的值。比如我们计算了一个题目，最后输出一句话，通常想写成这样：

`计算完毕，获胜概率为 x%，败北概率为 y%，平局概率为 (100-x-y)%。`

我们当然可以用很笨的拼装办法比如：


```python
x = 62
y = 29
print('计算完毕，获胜概率为 ' + str(x) + '%，败北概率为 ' + str(y) + '%，平局概率为 ' + str(100-x-y) + '%。')
```

    计算完毕，获胜概率为 62%，败北概率为 29%，平局概率为 9%。
    

这样的代码又难看又难维护，所以很多编程语言都提供了“参数化字符串”的方案，在 Python 3 里是所谓的 *f-string*，就是在字符串前面加一个小写的 `f`，下面是例子：


```python
x = 62
y = 29
print(f'计算完毕，获胜概率为 {x}%，败北概率为 {y}%，平局概率为 {100-x-y}%。')
```

    计算完毕，获胜概率为 62%，败北概率为 29%，平局概率为 9%。
    

这和上面的笨办法输出完全一样。所谓 *f-string* 就是一个字符串前面加个字母 f，也就是 `f'...'` 或者 `f"..."` 这样的。这样的字符串里可以用一对大括号括起来任何合法的表达式，这个表达式会被计算，并用结果替换 `{表达式}`。在上面的例子中，`{x}` 会用 `x` 的值 62 替换，`{y}` 会用 `y` 的值 29 替换，`{100-x-y}` 会用 `100-x-y` 的值 9 替换，最后就得到了我们想要的结果。

这个方案是不是漂亮多了呢？

顺便，在 *f-string* 中如果想输入大括号怎么办？使用 `{{` 和 `}}` 就可以了。

## 字符串处理

先看两个基本操作：字符串串接和字符串长度。


```python
hello = 'hello'
world = "world"
```


```python
# 可以用操作符 + 来串接字符串
hw = hello + ' ' + world + '!'
print(hw)
```

    hello world!
    


```python
# 内置函数 len() 能返回字符串的长度，这个函数还能返回别的类型的数据，以后我们会经常碰到它
len(hw)
```




    12



除了上面两个基本操作以外，Python 的字符串类 `str` 还提供了[大量的方法](https://docs.python.org/3/library/stdtypes.html#string-methods)来对字符串做各种处理，一些比较常用的包括：
* `capitalize()` 将字符串对象中每个句子的首字母大写；
* `upper()` 和 `lower()` 将字符串对象变成全大写和全小写；
* `rjust()` 将字符串扩展到指定长度，并向右对齐，也就是在原字符串左边补空格；
* `ljust()` 和 `center()` 类似，只是分别让原字符串左对齐（右边补空格）和居中（左右对称补空格）。

注意这一类方法都不会改变原来的字符串对象，而是把处理结果作为一个新的字符串返回，我们可以将其赋值给另一个变量然后使用。


```python
hw.capitalize()
```




    'Hello world!'




```python
hello.upper()
```




    'HELLO'




```python
hello.rjust(8)
```




    '   hello'




```python
hello.center(9)
```




    '  hello  '



`str` 类还提供了一组方法来判断字符串是不是具备特定特征，比如：
* `isalpha()` 判断字符串里是不是都是字母；
* `isdigit()` 判断字符串里是不是都是数字；
* `islower()` `isupper()` 判断字符串是不是全小写或者全大写字母。

这一类方法返回的都是 `True` 或者 `False`。


```python
hello.isalpha()
```




    True




```python
hello.isdigit()
```




    False




```python
'42'.isdigit()
```




    True



`str` 类还提供了一组方法来在字符串内查找特定子串：
* 可以用操作符 `in` 来检查字符串是不是包含某个子串；
* `find()` `index()` `rindex()` 可以查找子串并给出子串的位置，区别是：
    * 如果没找到 `find()` 会返回 `-1`，而后两个会抛出运行时异常 `ValueError`；
    * `index()` 返回从左向右找到的第一个匹配的位置，`rindex()` 则是从右向左找。
    
下面来看看例子。


```python
'Py' in 'Python'
```




    True




```python
'py' in 'Python' # 大小写敏感
```




    False




```python
s = 'Perform a string formatting operation. The string on which this method is called can contain literal text...'
```


```python
s.find('string')
```




    10




```python
s.find('abracadabra')
```




    -1




```python
s.index('string')
```




    10




```python
s.rindex('string')
```




    43




```python
# 下面这句会抛出 "ValueError: substring not found" 的异常
# s.rindex('abracadabra')
```

能查找子串，自然也能把找到的子串替换成其他字符串，这个方法叫 `replace`，这会对找到的所有子串进行替换，如果只希望替换一部分，可以传入一个“替换次数”的参数，那就只替换从左到右找到的前 n 个子串。和前面的例子一样，这个方法也不会改变原来的字符串对象，而是生成一个新的返回：


```python
hello.replace('l', '{ell}')
```




    'he{ell}{ell}o'




```python
hello.replace('l', '{ell}', 1)
```




    'he{ell}lo'



关于 Python 中的字符串操作就先介绍这些，其他的以后碰到再说，有兴趣也可以去看看[官方手册里的说明](https://docs.python.org/3.5/library/stdtypes.html#string-methods)，可以自己打开一个 notebook，建立几个字符串，然后对照官方手册里的方法一个个试试，会是个有趣的体验。

## 常用字符串

下面是 Python 内置的一些常用字符串，可以在我们的程序中直接使用（使用前需要先 `import`）：


```python
import string
```


```python
string.ascii_lowercase # 英文小写字母
```




    'abcdefghijklmnopqrstuvwxyz'




```python
string.ascii_uppercase # 英文大写字母
```




    'ABCDEFGHIJKLMNOPQRSTUVWXYZ'




```python
string.ascii_letters # 英文小写和大写字母
```




    'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ'




```python
string.digits # 十进制数字
```




    '0123456789'




```python
string.hexdigits # 十六进制数字
```




    '0123456789abcdefABCDEF'




```python
string.octdigits # 八进制数字
```




    '01234567'




```python
string.punctuation # 标点符号（英文）
```




    '!"#$%&\'()*+,-./:;<=>?@[\\]^_`{|}~'




```python
string.whitespace # 各种空白字符，包括空格、tab、换行、回车等等
```




    ' \t\n\r\x0b\x0c'




```python
string.printable # 上面所有字符的大合集
```




    '0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ!"#$%&\'()*+,-./:;<=>?@[\\]^_`{|}~ \t\n\r\x0b\x0c'



## 小结

* 了解字符集和字符编码（*optional*）；
* 了解 Python 中字符串的表示方法以及转义符；
* 了解如何使用 *f-string*；
* 了解 Python 中常用的字符串操作；
* 了解 `string` 模块中提供的常用字符串。