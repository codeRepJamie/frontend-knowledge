# 字符串相关知识

## 字符串方法(参数可接受正则)
- match 返回一个字符串匹配正则表达式的结果
- matchAll 返回一个包含所有匹配正则表达式的结果及分组捕获组的迭代器
- replace 返回一个由替换值（replacement）替换部分或所有的模式（pattern）匹配项后的新字符串
- replaceAll 方法返回一个新字符串，新字符串所有满足 pattern 的部分都已被replacement 替换
- search 返回正则表达式在字符串中首次匹配项的索引
- split 返回源字符串以分隔符(可以是正则)出现位置分隔而成的一个 Array

## match方法
空参数 返回[""]
无有任何匹配 返回 null

如果使用g标志 则将返回与完整正则表达式匹配的所有结果，但不会返回捕获组。
```js
'123'.match(/(\d)/g) // => ["1", "2", "3"]
```
如果未使用g标志，则仅返回第一个完整匹配及其相关的捕获组（Array） 与RegExp.exec(). 相同的结果
```js
'123'.match(/(\d)/) // =>  ["1", "1", index: 0, input: "123", groups: undefined]
```

## matchAll方法
输入一个使用g标志的正则表达式，利用 matchAll 会得到一个迭代器的返回值
相当于使用String.match()(不含g标志) 循环遍历字符串
```js
const matches = '123ghh12'.matchAll(/\d/g)
for (const match of matches) {
  console.log(match);
}
/*
["1", index: 0, input: "123ghh12", groups: undefined]
["2", index: 1, input: "123ghh12", groups: undefined]
["3", index: 2, input: "123ghh12", groups: undefined]
["1", index: 6, input: "123ghh12", groups: undefined]
["2", index: 7, input: "123ghh12", groups: undefined]
*/
```