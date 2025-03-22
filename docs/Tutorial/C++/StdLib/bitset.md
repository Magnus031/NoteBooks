# <center>bitset</center>


## Introduce 
[std::bitset](https://en.cppreference.com/w/cpp/utility/bitset) is a class template that represents a fixed-size sequence of N bits. It is a specialized container class that is designed to store bits. It is a part of the C++ Standard Library.

简单的来说，就是提供的一个固定大小的位集合，可以用来存储位。并且支持一些常见的位操作。在遇到的经常需要处理位运算的时候，可以使用这个类。


## Usage 使用方法

> 首先，这个 `bitset` 类是在 `<bitset>` 头文件中的，所以我们需要引入这个头文件才能使用

### 构造函数

```cpp
// 构造函数
bitset(); // 默认构造，所有位初始化为 0
bitset(unsigned long val); // 用整数 val 初始化
bitset(const string& str, size_t pos = 0, size_t n = string::npos); // 用字符串初始化
```

正如上述的代码所示，`bitset` 支持三种构造函数，分别是默认构造函数，用整数初始化，用字符串初始化。其实很简单的理解就是，这个`bitset`可以将整数或者字符串转换为二进制的形式。


### 常见的位运算操作

下面是一些常见的成员函数，方便我们对这个类对象进行位操作。

```cpp
// 位操作

set()   // 将所有位设置为 1
reset() // 将所有位设置为 0
flip()  // 将所有位取反
test(size_t pos) // 测试 pos 位是否为 1
operator[] // 重载 [] 运算符，可以直接访问某一位

// 统计与查询
count() // 统计 1 的个数
size()  // 返回位数
any()   // 是否有 1
none()  // 是否没有 1
all()   // 是否全是 1

// 转换函数
to_ulong() // 转换为 unsigned long
to_ullong() // 转换为 unsigned long long
to_string() // 转换为字符串 （如 "001011"）

```

### 使用示例

```cpp
#include <bitset>
#include <iostream>

int main() {
    // 构造函数
    std::bitset<8> b1(42);      // 二进制: 00101010
    std::bitset<8> b2("1100");  // 二进制: 00001100

    // 位操作
    b1.set(0);      // 00101011
    b2.flip();      // 11110011

    std::cout << b1 << std::endl; // 输出: 00101011
    std::cout << b2.count() << std::endl; // 输出 1 的个数: 6

    // 字符串转换
    std::bitset<4> b("1010");
    std::string s = b.to_string(); // s = "1010"
    unsigned long x = b.to_ulong(); // x = 10
}

```

## 注意事项

我们观察上面的初始化函数，其实不难发现，这个`bitset`的储存方式是从低位到高位的，也就是说是小端逆序的。这个在使用的时候需要注意。如果要从左往右遍历，就应该从高位(`this.size()-1`)开始遍历。