# 拯救一切强迫症 - 读《编写可维护的 JavaScript》（一）

本文写于 2020 年 4 月 24 日

我在小学的时候就有接触过编程，所以读大一的时候 C 语言还算是轻车熟路。自然会有很多同学给我看他们的代码，麻烦我帮助他们找一找 bug。

我代码拿到手的第一件事儿是啥？

重排代码格式！（相信大家基本上大学学 C 语言都用的是 VC++，并没有代码自动格式化功能，现在我极力推荐 prettier 进行自动格式化）

通常我看到的代码都是这样的：

```javascript
if (wl && wl.length) {
	                for (i = 0, l = wl.length; i < l; ++i) {
		    p = wl[i]
		      type = Y.Lang.type(r[p])
		    if (s.hasOwnProperty(p)) {if (merge && type == 'object') {
			Y.mix(r[p], s[p])
			} else if (ov || !pinr) {
				        r[p] = s[p]
	}
		      }
	        }
    }
```

这种代码人类几乎没有办法读懂！

老话说得好：代码是给人读的，只是偶尔给机器看一下。

让我们来重置一下缩进：

```JavaScript
if(wl && wl.length){
  for(i=0, l=wl.length; i < l; ++i) {
    p = wl[i];
    type = Y.Lang.type(r[p]);
    if(s.hasOwnProperty(p)){
      if(merge && type == 'object'){
        Y.mix(r[p], s[p]);
      } else if (ov || !(pinr)){
        r[p] = s[p];
      }
    }
  }
}
```

代码瞬间变得易于阅读了！

## 01 缩进

接下来我们来看一看书中对于缩进的观点。

**使用制表符缩进**

每一个缩进层级都代表一个单独的制表符。也就是一个制表符一个缩进，两个制表符两个缩进，以此类推。

他的好处在于，编辑器可以直接修改制表符的长度，可长可短，随心所欲。

但是缺点在于系统对于制表符的解释不一样。我们在 windows、macOS、Linux 中看到的制表符可能都不一样。

**使用空格缩进**

有几种做法：2 个空格表示一个缩进、4 个空格表示一个缩进、8 个空格表示一个缩进。

很多团队经常会选择 4 个空格，因为这是一种折中的方案。

在文本编辑器中，我们可以配置在敲击 tab 时，自动插入 n 个空格，这样，不管在哪里打开，代码都会是一样的。

我们看一些著名团队的选择——

**制表符：**

- jQuery
- Dojo

**空格：**

- Dauglas Crockford 使用 4 个空格字符的缩进；
- Sprout Core 使用 2 个空格的缩进；
- Goolge 的 JavaScript 使用 2 个空格的缩进。

作者个人推荐使用四个空格作为一个缩进层级。

无论如何，选择了其中一种，就一直用下去，千万不要在一个文件里混用！

## 02 语句结尾

作者认为应该加分号，但是我个人不喜欢加分号。

JS 又一个叫做 Automatic Semicolon Insertion 机制，叫做自动分号插入机制。但是 ASI 的规则非常非常的复杂，所以我们很难预料他会在哪里插入分号，包括 jQuery、Google、Dojo 都是推荐使用分号结尾。

## 03 一行的长度

很多很多的编程规范提到过，一行长度不宜超过 80。

这个规则是因为当年的文本编辑器，单行字数限制就是 80。

但是现在，即使我们的屏幕宽了、分辨率高了，还是推荐大家使用 80 的长度限制。

看看大公司的规范：

- Java 语言编程规范，80；
- Android 开发者编码风格指南，80；
- 非官方的 Ruby 编程规范：80；
- python，79。

在超过了 80 的长度之后就需要换行了，比如这样：

```JavaScript
callAFunction(document, element, window, "somestringvalue", true, 123,
              navigator);
```

**在换行后应该追加一个缩进**

要注意，不要让运算符或者逗号换行了：

```JavaScript
callAFunction(document, element, window, "somestringvalue", true, 123
              , navigator);
```

## 04 空行

为了保证代码的可读性，我们会适当的空上几行。

```JavaScript
if(wl&&wl.length){
  for(i = 0, l = wl.length; i<l; ++i) {
    p = wl[i];
    type = Y.Lang.type(r[p]);
    if(s.hasOwnProperty(p)){
      if(merge&&type=='object') {
        Y.mix(r[p],s[p]);
      } else if (ov || !(pinr)) {
        r[p]=s[p];
      }
    }
  }
}
```

```javascript
if (wl && wl.length) {
	for (i = 0, l = wl.length; i < l; ++i) {
		p = wl[i]
		type = Y.Lang.type(r[p])

		if (s.hasOwnProperty(p)) {
			if (merge && type == 'object') {
				Y.mix(r[p], s[p])
			} else if (ov || !pinr) {
				r[p] = s[p]
			}
		}
	}
}
```

（未完待续）