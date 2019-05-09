# enable\_if 小计

**enable\_if **的主要作用就是当某个 _condition _成立时，**enable\_if **可以提供某种类型。

Enable type if the condition is met.

The type T is enabled as member type enable\_if::type if Cond is true. Otherwise, enable\_if::type is not defined.

SFINAE:

**SFINAE**是C++ 的一种语言属性，具体内容就是”从一组重载函数中删除模板实例化无效的函数”。

如下代码所示：

```
long multiply(int i, int j) { return i * j; }
template <class T> 
typename T::multiplication_result multiply(T t1, T t2)
{
    return t1 * t2;
}
int main(void)
{
    multiply(4, 5);
}
```

main 函数调用 multiply 会使编译器会尽可能去匹配所有候选函数，虽然第一个 multiply 函数明显是较优的匹配，但是为了得到一个最精确的匹配，编译器依然会尝试去匹配剩下的候选函数，此时就会去推导 第二个multiply 函数，中间在参数推导的过程中出现了一个无效的类型 int::multiplication\_result ，但是因为 SFINAE 原则并不会报错。





#### std::enable\_if&lt;&gt; 的实现

前面我们在介绍 std::enable\_if 的时候提到，如果 condition 不满足的话，会生成一个无效的类型，此处由于 SFINAE 机制的存在，只要 call 存在一个匹配的话，就不会报错（只是简单的丢弃该函数）。

std::enable\_if&lt;&gt;的实现机制如下代码所示：

```
template<bool Cond, typename T = void> struct enable_if {};
template<typename T> struct enable_if<true, T> { typedef T type; };
```

从上面的代码可以看到，在 condition 为真的时候，由于偏特化机制，第二个结构体模板明显是一个更好的匹配，所以 std::enable\_if&lt;&gt;::type 就是有效的。当 condition 为假的时候，只有第一个结构体模板能够匹配，所以 std::enable\_if&lt;&gt;::type 是无效的，会被丢弃。

--------------------- 

作者：电影旅行敲代码 

来源：CSDN 

原文：https://blog.csdn.net/dashuniuniu/article/details/51705874 

版权声明：本文为博主原创文章，转载请附上博文链接！

