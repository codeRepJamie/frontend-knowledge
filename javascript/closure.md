# 闭包相关知识点

## 闭包定义
闭包就是能够读取其他函数内部变量的函数。

## 闭包应用场景
 - 把一个函数当参数给另一个函数执行 (回调函数)
 - 定义一个函数体内返回另一个函数 （单例模式）

## 闭包注意事项
 - 由于闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包，否则会造成网页的性能问题，在IE中可能导致内存泄露。解决方法是，在退出函数之前，将不使用的局部变量全部删除。
 - 闭包会在父函数外部，可以改变父函数内部变量的值。所以，不要随便改变父函数内部变量的值，只能读取，尽量不要修改。