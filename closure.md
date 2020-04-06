1、js垃圾回收机制
```
js 中的 变量 函数 不再使用后，会被自动js垃圾回收机制回收
```
2、形成闭包条件
```
条件一： 函数内部嵌套函数 
条件二： 内部函数引用外部函数的 变量 参数 
使用 return 返回了 此内部函数,上面的 变量 和参数 不会被回收
```
3:示例
```
function fn(x){
    var a = 5;
   function innerFn(){
       console.log(x);
    console.log(a);
    }
   return innerFn;
}
var c = fn(20);
c();// 20 //5
```
