---
title: Python数据处理的一些技巧
date: 2017-02-16 14:57:05
tags: [Python]
categories: [Python]
---


### maketrans
Python maketrans() 方法用于创建字符映射的转换表，对于接受两个参数的最简单的调用方式，
第一个参数是字符串，表示需要转换的字符，第二个参数也是字符串表示转换的目标。
注：两个字符串的长度必须相同，为一一对应的关系。

Python3.4已经没有string.maketrans()了，取而代之的是内建函数:
`bytearray.maketrans()`、`bytes.maketrans()`、`str.maketrans()`
```Python
intab = "abcd"  
outtab = "1234"  
str_trantab = str.maketrans(intab,outtab)  

test_str = "csdn blog: http://blog.csdn.net/wirelessqa"  

print (test_str.translate(str_trantab))
```

### re.sub() 正则替换
对于输入的一个字符串，利用正则表达式（的强大的字符串处理功能），去实现（相对复杂的）字符串替换处理，然后返回被替换后的字符串


Python的正则替换功能强大，用法相对复杂，这里列举一下。
`re.sub(pattern, repl, string, count=0, flags=0)`
> Return the string obtained by replacing the leftmost non-overlapping occurrences of pattern in string by the replacement `repl`. If the pattern isn’t found, `string` is returned unchanged. `repl` can be a string or a function; if it is a string, any backslash escapes in it are processed. That is, `\n` is converted to a single newline character, `\r` is converted to a carriage return, and so forth. Unknown escapes such as `\&` are left alone. Backreferences, such as `\6`, are replaced with the substring matched by group 6 in the pattern.

re.sub共有五个参数。
其中三个必选参数：`pattern`, `repl`, `string`
两个可选参数：`count`, `flags`

`re.sub`是个正则表达式方面的函数，用来实现通过正则表达式，实现比普通字符串的`replace`更加强大的替换功能；

```Python
inputStr = "hello 111 world 111"
```
可以通过
```Python
replacedStr = inputStr.replace("111", "222")
```
换成
```Python
"hello 222 world 222"
```
但是，如果输入字符串：
```Python
inputStr = "hello 123 world 456"
```
想要把123和456都替换为同样的数，通过replace就比较复杂了，然而通过正则表达式，就可以简单的实现：
```python
replacedStr = re.sub('\d+', '222', inputStr)
```

**参数：pattern**
表示正则中的模式字符串
需要知道的是：
反斜杠加数字（`\N`），则对应着匹配的组（matched group）
比如`\6`，表示匹配前面pattern中的第6个group

比如要处理：
```
hello flame, nihao flame
```
要求把flame替换为同样的字符串peak，可以这样实现
```python
inputStr = "hello flame, nihao flame"
replacedStr = re.sub(r"hello (\w+), nihao \1", "peak", inputStr) #注意不加r， 会出错，必须加r使其成为pattern
print (replacedStr)#输出： 'peak'
```

**参数：repl**
列个网站： [pythex](http://pythex.org/):可以在线调试正则文本
[Regex101](https://regex101.com/)

即replacement， 被替换的字符
`repl`可以是字符串，也可以是函数。

如果repl是字符串的话，其中的任何反斜杠转义字符，都会被处理的。即：
+ `\n`：会被处理为对应的换行符；
+ `\r`：会被处理为回车符；
+ 其他不能识别的转移字符，则只是被识别为普通的字符：
+ 比如`\j`，会被处理为j这个字母本身；
+ 反斜杠加g以及中括号内一个名字，即：`\g<name>`，对应着命了名的组，named group

如果想要把`hello flame, nihao flame`中的flame提取出来：
```Python
>>> inputStr = "hello flame, nihao flame"
>>> re.sub(r"hello (\w+), nihao \1", "\g<1>", inputStr)
'flame'
```
对应的带命名的组（named group）的版本是：
```Python
>>> inputStr = "hello flame, nihao flame";
>>> replacedStr = re.sub(r"hello (?P<name>\w+), nihao (?P=name)", "\g<name>", inputStr);
'flame'

```
看一个实际的例子
```
新闻:<ahref=\"http://xueqiu.com/S/SH600633\"target=\"_blank\">$浙报传媒(SH600633)$</a>下属全资子公司上海分众软件技术有限公司近日获得
```
这是一段文字，我们需要将中间的html链接去掉，但是需要留下其中的公司名称与代码，达到如下目的：
```
新闻:浙报传媒(SH600633)下属全资子公司上海分众软件技术有限公司近日获得
```
这里就可以用re.sub()以及name group来实现：
```Python
>>> inputStr = '新闻:<ahref=\"http://xueqiu.com/S/SH600633\"target=\"_blank\">$浙报传媒(SH600633)$</a>下属全资子公司上海分众软件技术有限公司近日获得'
>>> re.sub(r"(<ahref=.+>\$)(.*)(\$</a>)", "\g<2>",inputStr)
'新闻:浙报传媒(SH600633)下属全资子公司上海分众软件技术有限公司近日获得'
```
就是数括号的个数啦！

**参数：string**
要被处理的字符串


### Python 判断变量类型时，为什么不推荐使用type()方法
[为什么不推荐使用type()方法](https://segmentfault.com/q/1010000000127305)

实际上还有一种方法是用`isinstance` 比如：
```python
a = 111
isinstance(a, int)
True
```
`isinstance` 和 `type` 的区别在于：

```Python
class A:
    pass

class B(A):
    pass

isinstance(A(), A)  # returns True
type(A()) == A      # returns True
isinstance(B(), A)    # returns True
type(B()) == A        # returns False
```

区别就是 对于subclass之类的 type就不行事了


### Testing if a Variable Is Defined
在抓取数据的时遇到一个问题，有些时候因为未知的原因，数据未获取成功，接下来的代码会提示`variable used before defined`类似错误。
解决方案如下：

In Python, all variables are expected to be defined before use. The `None` object is a value you often assign to signify that you have no real value for a variable, as in:
```python
try:
  x
except NameError:
  x = None
```

Then it’s easy to test whether a variable is bound to `None`:
```Python
if x is None:
    some_fallback_operation(  )
else:
    some_operation(x)
```

Python doesn’t have a specific function to test whether a variable is defined, since all variables are expected to have been defined before use, even if initially assigned the `None` object. Attempting to access a variable that hasn’t previously been defined raises a `NameError` exception (which you can handle with a `try/except` statement, as you can for any other Python exception).
```Python
try:
    x
    some_operation(x)
except NameError:
    some_fallback_operation(  )
```


### 

使用 `dir()`，`vars()` 或者 `inspect` 模块来获取你对感兴趣的内容。

```Python
from PIL import Image

orig_image = Image.open('1.gif')
print orig_image.info
print dir(orig_image)
print vars(orig_image)
print orig_image.size
```

输出结果：

```bash
{'background': 53, 'extension': ('NETSCAPE2.0', 411L), 'version': 'GIF89a', 'transparency': 53, 'duration': 100, 'loop': 0}

['_GifImageFile__fp', '_GifImageFile__frame', '_GifImageFile__offset', '_GifImageFile__rewind', '_Image__transformer', '__array_interface__', '__class__', '__copy__', '__delattr__', '__dict__', '__doc__', '__enter__', '__eq__', '__exit__', '__format__', '__getattribute__', '__getstate__', '__hash__', '__init__', '__module__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__setstate__', '__sizeof__', '__str__', '__subclasshook__', '__weakref__', '_close_exclusive_fp_after_loading', '_copy', '_crop', '_dump', '_exclusive_fp', '_expand', '_is_animated', '_n_frames', '_new', '_open', '_prev_im', '_repr_png_', '_seek', 'alpha_composite', 'category', 'close', 'convert', 'copy', 'crop', 'data', 'decoderconfig', 'decodermaxblock', 'disposal_method', 'dispose', 'dispose_extent', 'draft', 'effect_spread', 'filename', 'filter', 'format', 'format_description', 'fp', 'frombytes', 'fromstring', 'getbands', 'getbbox', 'getcolors', 'getdata', 'getextrema', 'getim', 'getpalette', 'getpixel', 'getprojection', 'global_palette', 'height', 'histogram', 'im', 'info', 'is_animated', 'load', 'load_end', 'load_prepare', 'mode', 'n_frames', 'offset', 'palette', 'paste', 'point', 'putalpha', 'putdata', 'putpalette', 'putpixel', 'pyaccess', 'quantize', 'readonly', 'remap_palette', 'resize', 'rotate', 'save', 'seek', 'show', 'size', 'split', 'tell', 'thumbnail', 'tile', 'tobitmap', 'tobytes', 'toqimage', 'toqpixmap', 'tostring', 'transform', 'transpose', 'verify', 'width']

{'_GifImageFile__rewind': 397L, '_GifImageFile__fp': <open file '1.gif', mode 'rb' at 0x0000000002ABD540>, 'dispose_extent': (0, 0, 619, 347), '_GifImageFile__frame': 0, 'im': None, 'decodermaxblock': 65536, '_exclusive_fp': True, 'size': (619, 347), 'category': 0, 'palette': <PIL.ImagePalette.ImagePalette object at 0x0000000002D94080>, '_is_animated': None, 'dispose': None, 'filename': '1.gif', 'readonly': 1, 'tile': [('gif', (0, 0, 619, 347), 435L, (7, False))], 'fp': <open file '1.gif', mode 'rb' at 0x0000000002ABD540>, 'disposal_method': 1, 'pyaccess': None, 'decoderconfig': (), '_n_frames': None, 'info': {'background': 53, 'extension': ('NETSCAPE2.0', 411L), 'version': 'GIF89a', 'transparency': 53, 'duration': 100, 'loop': 0}, '_prev_im': None, '_GifImageFile__offset': 435L, 'global_palette': <PIL.ImagePalette.ImagePalette object at 0x0000000002D940B8>, 'mode': 'P'}

(619, 347)
```
