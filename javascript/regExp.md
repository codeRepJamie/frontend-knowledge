# 正则表达式

## 特殊字符
- ^ : 匹配字符串的开头；
- $ : 匹配字符串的结尾；
- \* ; 出现零次到多次；
- \+ ：出现一次到多次；
- ？: 出现零次或一次；
- . : 除了\n以外的任意字符
- {n} : 出现n次；
- {n,m}: 出现n到m次；
- {n,}: 出现n次到多次
- \\b: 单词的边界
- \\B: 非单词的边界
- \\d: 数字, 等价于[0-9]
- \\D: 非数字, 等价于[^0-9]
- \\f: 换页符
- \\n: 换行符
- \\r: 回车符
- \\s: 空白字符
- \\S: 非空白字符
- \\w: 单字字符, 等价于[A-Za-z0-9]
- \\W: 非单字字符, 等价于[^A-Za-z0-9]

## 其他特殊字符
- \\n: (n表示1,2,3,4...) 返回最后的第n个子捕获匹配的子字符串(捕获的数目以左括号计数)。

## 非捕获组(?:x)
匹配 'x' 但是不记住匹配项。这种括号叫作非捕获括号
捕获组匹配结果上与非捕获组结果相同， (?:x)非捕获组的语法是在捕获组的基础上，在左括号的右侧加上?:

作用
1. 不需要用到分组里面的内容的时候，用非捕获组，主要是为了提升效率
2. 用在可选分支的时候，当我们不需要分组里面的数据的时候，也可以用非捕获组

## 先行断言 Positive lookahead x(?=y) 
匹配'x'仅仅当'x'后面跟着'y'.这种叫做先行断言

例如，/Jack(?=Sprat)/会匹配到'Jack'仅当它后面跟着'Sprat'。/Jack(?=Sprat|Frost)/匹配‘Jack’仅当它后面跟着'Sprat'或者是‘Frost’。但是‘Sprat’和‘Frost’都不是匹配结果的一部分。
```
/Jack(?=Sprat|Frost)/
'JackSprat' => 'Jack'
'JackFrost' => 'Jack'
'JackOlive' => x
```

## 后行断言 Positive lookbehind (?<=y)x (es2018/ES9)
匹配'x'仅当'x'前面是'y'.这种叫做后行断言。(注意仅部分浏览器支持)

例如，/(?<=Jack)Sprat/会匹配到' Sprat '仅仅当它前面是' Jack '。/(?<=Jack|Tom)Sprat/匹配‘ Sprat ’仅仅当它前面是'Jack'或者是‘Tom’。但是‘Jack’和‘Tom’都不是匹配结果的一部分。
```
/(?<=Jack|Tom)Sprat/
'JackSprat' => 'Sprat'
'TomSprat' => 'Sprat'
'MarkSprat' => x
```

## 正向否定查找 Negative lookahead x(?!y)
先行断言的取反
```
/Jack(?!Sprat|Frost)/
'JackSprat' => x
'JackFrost' => x
'JackOlive' => 'Jack'
```

## 反向否定查找 Negative lookbehind (?<!y)x (es2018/ES9)
后行断言的取反
```
/(?<!Jack|Tom)Sprat/
'JackSprat' => x
'TomSprat' => x
'MarkSprat' => 'Sprat'
```