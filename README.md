regex
--
正则表达式是匹配模式，要么匹配字符，要么匹配位置。如果正则只有精确匹配是没多大意义的，比如 /hello/，也只能匹配字符串中的 "hello" 这个子串。
```javascript
	var regex = /hello/;
	console.log( regex.test("hello") );
	// => true
```
模糊匹配，有两个方向上的“模糊”：横向模糊和纵向模糊。

横向模糊匹配
-
横向模糊指的是，一个正则可匹配的字符串的长度不是固定的，可以是多种情况的。其实现的方式是使用量词。譬如 {m,n}，表示连续出现最少 m 次，最多 n 次。
比如正则 /ab{2,5}c/ 表示匹配这样一个字符串：第一个字符是 "a"，接下来是 2 到 5 个字符 "b"，最后是字符 "c"。
```javascript
	var regex = /ab{2,5}c/g;
	var string = "abc abbc abbbc abbbbc abbbbbc abbbbbbc";
	console.log( string.match(regex) );
	// => ["abbc", "abbbc", "abbbbc", "abbbbbc"]
```

纵向模糊匹配
-
纵向模糊指的是，一个正则匹配的字符串，具体到某一位字符时，它可以不是某个确定的字符，可以有多种可能。其实现的方式是使用字符组。譬如 [abc]，表示该字符是可以字符 "a"、"b"、"c" 中的任何一个。

如果字符组里的字符特别多的话，怎么办？可以使用`范围表示法`。
比如 [123456abcdefGHIJKLM]，可以写成 [1-6a-fG-M]。用连字符 - 来省略和简写。

纵向模糊匹配，还有一种情形就是，某位字符可以是任何东西，但就不能是 "a"、"b"、"c"。此时就是排除字符组（反义字符组）的概念。例如 [^abc]，表示是一个除 "a"、"b"、"c"之外的任意一个字符。字符组的第一位放 ^（脱字符），表示求反的概念。

常见的简写形式
-
有了字符组的概念后，一些常见的符号我们也就理解了。因为它们都是系统自带的简写形式。`\d`表示 [0-9]。表示是一位数字。记忆方式：其英文是 digit（数字）。
`\D`表示 [^0-9]。表示除数字外的任意字符。`\w`表示 [0-9a-zA-Z_]。表示数字、大小写字母和下划线。记忆方式：w 是 word 的简写，也称单词字符。`\W`表示 [^0-9a-zA-Z_]。非单词字符。`\s`表示 [ \t\v\n\r\f]。表示空白符，包括空格、水平制表符、垂直制表符、换行符、回车符、换页
符。记忆方式：s 是 space 的首字母，空白符的单词是 white space。`\S`表示 [^ \t\v\n\r\f]。 非空白符。`.`表示 [^\n\r\u2028\u2029]。通配符，表示几乎任意字符。换行符、回车符、行分隔符和段分隔符
除外。

贪婪匹配与惰性匹配
-
```javascript
	var regex = /\d{2,5}/g;
	var string = "123 1234 12345 123456";
	console.log( string.match(regex) );
	// => ["123", "1234", "12345", "12345"]
```
其中正则 /\d{2,5}/，表示数字连续出现 2 到 5 次。会匹配 2 位、3 位、4 位、5 位连续数字。
但是其是贪婪的，它会尽可能多的匹配。你能给我 6 个，我就要 5 个。你能给我 3 个，我就要 3 个。
而惰性匹配，就是尽可能少的匹配：
```javascript
	var regex = /\d{2,5}?/g;
	var string = "123 1234 12345 123456";
	console.log( string.match(regex) );
	//第二、三个字符串命中了两组，第四个字符串命中了3组
	// => ["12", "12", "34", "12", "34", "12", "34", "56"]
```
其中 /\d{2,5}?/ 表示，虽然 2 到 5 次都行，当 2 个就够的时候，就不再往下尝试了。

多选分支
-
一个模式可以实现横向和纵向模糊匹配。而多选分支可以支持多个子模式任选其一。具体形式如下：(p1|p2|p3)，其中 p1、p2 和 p3 是子模式，用 |（管道符）分隔，表示其中任何之一。例如要匹配字符串 "good" 和 "nice" 可以使用 /good|nice/。
但有个事实我们应该注意，比如我用 /good|goodbye/，去匹配 "goodbye" 字符串时，结果是 "good"：
```javascript
	var regex = /good|goodbye/g;
	var string = "goodbye";
	console.log( string.match(regex) );
	// => ["good"]
	
	var regex = /goodbye|good/g;
	var string = "goodbye";
	console.log( string.match(regex) );
	// => ["goodbye"]
```
分支结构也是惰性的，即当前面的匹配上了，后面的就不再尝试了。

匹配 16 进制颜色值
-
要求匹配
```
	#ffbbad
	#Fc01DF
	#FFF
	#ffE
```
正则如下:
```javascript
	var regex = /#([0-9a-fA-F]{6}|[0-9a-fA-F]{3})/g;
	var string = "#ffbbad #Fc01DF #FFF #ffE";
	console.log( string.match(regex) );
	// => ["#ffbbad", "#Fc01DF", "#FFF", "#ffE"]
```

匹配时间
-
要求匹配：
```
	23:59
	02:07
```
正则如下：
```javascript
	var regex = /^([01][0-9]|[2][0-3]):[0-5][0-9]$/;
	console.log( regex.test("23:59") );
	console.log( regex.test("02:07") );
	// => true
	// => true
```
如果也要求匹配 "7:9"，也就是说时分前面的 "0" 可以省略。
```javascript
	var regex = /^(0?[0-9]|1[0-9]|[2][0-3]):(0?[0-9]|[1-5][0-9])$/;
	console.log( regex.test("23:59") );
	console.log( regex.test("02:07") );
	console.log( regex.test("7:9") );
	// => true
	// => true
	// => true
```

匹配日期
-
要求匹配：
```
	2017-06-10
```
正则如下：
```javascript
	var regex = /^[0-9]{4}-(0[1-9]|1[0-2])-(0[1-9]|[12][0-9]|3[01])$/;
	console.log( regex.test("2017-06-10") );
	// => true
```

window 操作系统文件路径
-
要求匹配：
```
	F:\study\javascript\regex\regular expression.pdf
	F:\study\javascript\regex\
	F:\study\javascript
	F:\
```
整体模式是:
```
	盘符:\文件夹\文件夹\文件夹\
```
其中匹配 "F:\"，需要使用[a-zA-Z]:\\，其中盘符不区分大小写，注意 \ 字符需要转义。
文件名或者文件夹名，不能包含一些特殊字符，此时我们需要排除字符组 `[^\\:*<>|"?\r\n/]`来表示合法
字符。
另外它们的名字不能为空名，至少有一个字符，也就是要使用量词 +。因此匹配 文件夹\，可用
`[^\\:*<>|"?\r\n/]+\\`。
正则如下：
```javascript
	var regex = /^[a-zA-Z]:\\([^\\:*<>|"?\r\n/]+\\)*([^\\:*<>|"?\r\n/]+)?$/;
	console.log( regex.test("F:\\study\\javascript\\regex\\regular expression.pdf") );
	// => true
```
`*`代表任意次

匹配 id
-
要求从
```
	<div id="container" class="main"></div>
```
提取出 id="container"。
可能刚开始想到的正则是
```javascript
	var regex = /id=".*"/
```
但是因为`.`是通配符，本身就匹配双引号的，而量词`*`又是贪婪的，当遇到 container 后面双引号时，是不会
停下来，会继续匹配，直到遇到最后一个双引号为止。
解决之道，可以使用惰性匹配：
```javascript
	var regex = /id=".*?"/
	var string = '<div id="container" class="main"></div>';
	console.log(string.match(regex)[0]);
	// => id="container"
```
当然，这样也会有个问题。效率比较低，因为其匹配原理会涉及到“回溯”这个概念，可以把"号去掉，优化如下：
```javascript
	var regex = /id="[^"]*"/
	var string = '<div id="container" class="main"></div>';
	console.log(string.match(regex)[0]);
	// => id="container"
```

位置匹配
-
正则表达式是匹配模式，要么匹配字符，要么匹配位置。位置（锚）是相邻字符之间的位置，在 ES5 中，共有 6 个锚：
```
	^、$、\b、\B、(?=p)、(?!p)
```
^（脱字符）匹配开头，在多行匹配中匹配行开头。
$（美元符号）匹配结尾，在多行匹配中匹配行结尾。比如我们把字符串的开头和结尾用 "#" 替换（位置可以替换成字符的）：
```javascript
	var result = "hello".replace(/^|$/g, '#');
	console.log(result);
	// => "#hello#"
```
多行匹配模式（即有修饰符 m）时，二者是行的概念，这一点需要我们注意（\n表示换行）：
```javascript
	var result = "I\nlove\njavascript".replace(/^|$/gm, '#');
	console.log(result);
	/*
	#I#
	#love#
	#javascript#
	*/
```
\b 是单词边界，具体就是 \w 与 \W 之间的位置，也包括 \w 与 ^ 之间的位置，和 \w 与 $ 之间的位置。
比如考察文件名 "[JS] Lesson_01.mp4" 中的 \b，如下：
```javascript
	var result = "[JS] Lesson_01.mp4".replace(/\b/g, '#');
	console.log(result);
	// => "[#JS#] #Lesson_01#.#mp4#"
```
\B 就是 \b 的反面的意思，非单词边界。例如在字符串中所有位置中，扣掉 \b，剩下的都是 \B 的。
比如上面的例子，把所有 \B 替换成 "#"：
```javascript
	var result = "[JS] Lesson_01.mp4".replace(/\B/g, '#');
	console.log(result);
	// => "#[J#S]# L#e#s#s#o#n#_#0#1.m#p#4"
```
(?=p)，其中 p 是一个子模式，即 p 前面的位置，或者说，该位置后面的字符要匹配 p
```javascript
	var result = "hello".replace(/(?=l)/g, '#');
	console.log(result);
	//l前面的位置
	// => "he#l#lo"
```
而 (?!p) 就是 (?=p) 的反面意思，比如：
```javascript
	var result = "hello".replace(/(?!l)/g, '#');
	console.log(result);
	// => "#h#ell#o#"
```
二者的学名分别是 positive lookahead 和 negative lookahead。
中文翻译分别是正向先行断言和负向先行断言。
也有书上把这四个东西，翻译成环视，即看看右边和看看左边。

位置的特性
-
对于位置的理解，我们可以理解成空字符 ""。
```
	"hello" == "" + "h" + "" + "e" + "" + "l" + "" + "l" + "o" + "";	
```
也等价于：
```
	"hello" == "" + "" + "hello"	
```
因此，把 /\^hello$/ 写成 /^^hello$$$/，是没有任何问题的：
```javascript
	var result = /^^hello$$$/.test("hello");
	console.log(result);
	// => true
```

数字的千位分隔符表示法
-
比如把 "12345678"，变成 "12,345,678"。
可见是需要把相应的位置替换成 ","。
```javascript
	var result = "12345678".replace(/(?=\d{3}$)/g, ',')
	console.log(result);
	// => "12345,678"
```
(?=\d{3}$) 匹配 \d{3}$ 前面的位置。而 \d{3}$ 匹配的是目标字符串最后那 3 位数字。
因为逗号出现的位置，要求后面 3 个数字一组，也就是 \d{3} 至少出现一次。
此时可以使用量词 +：
```javascript
	var result = "12345678".replace(/(?=(\d{3})+$)/g, ',')
	console.log(result);
	// => "12,345,678"
```
上面的正则，仅仅表示把从结尾向前数，一但是 3 的倍数，就把其前面的位置替换成逗号。因此会出
现问题。
怎么解决呢？我们要求匹配的到这个位置不能是开头。
```javascript
	var regex = /(?!^)(?=(\d{3})+$)/g;
	var result = "12345678".replace(regex, ',')
	console.log(result);
	// => "12,345,678"
```	
