# C++中的基本语法

## C++中的格式化输出

需要包含iomanip头文件

* fixed 以普通小数形式输出浮点数
  * +setprecision(n)可以控制输出几位小数
  * 在使用fixed方式输出下，n是小数点后面保留的位数
* setw(w) 指定输出宽度为w个字符，或者输入字符串时读入w个字符
* 作用是一次性的
* **也同时作用于cin控制输入字符数量**
* setfill(c) 在指定宽度的情况下，输出的宽度不足时，用字符c填充，默认是空格
* setiosflags(ios::|ios::)可以设置多个标签
* resetiosflags(ios::|ios::)设置标签之后可以去除标签

## C++控制输入

* 普通cin在接收到空格 制表符 回车都会结束

* cin.get(字符数组名,接收字符数目)用来接收一行字符串,可以接收空格

* string str; getline(cin,str); 输入一串字符串

## stack的使用

\#include\<stack>

1. 声明一个栈 stack\<int> stk;
   * stk.push(): 向栈内压入一个成员；

   * stk.pop(): 从栈顶弹出一个成员；

   * stk.empty(): 如果栈为空返回true，否则返回false；

   * stk.top(): 返回栈顶，但不删除成员；

   * stk.size(): 返回栈内元素的大小

## queue的使用

\#include\<queue>

1. 声明一个对列 queue\<int> q;

   * q.push(x): 将x入队,x进入队尾。

   * q.pop(): 队首出队 注意，并不会返回被弹出元素的值。

   * q.front(): 访问队首元素，即最早被压入队列的元素。

   * q.back():访问队尾元素，即最后被压入队列的元素。

   * q.empty():判断队列空，当队列空时，返回true。

## vector的使用

相当于在数组之上的封装

\#include\<vector>

```c
int arr[5] = {0,1,2,3,4};
vector<int> var(arr,arr+5);//将arr数组的元素用于初始化向量
var.size(); //向量大小
var.begin(); //开始指针
var.end(); //结束指针

var[0]; //下标访问
var.at(1); //at方法访问

var.resize(); //更改向量大小
var.empty(); //判断是否为空

//遍历
vector<int>::iterator it;
for (it = vec.begin(); it != vec.end(); it++)
    cout << *it << endl;
//或者
for (auto iter = vec.begin(); iter != vec.end;++iter)
{
  if (*iter == 10)
    nums.erase(iter);
}
```

## C++中set的使用

set的特性是，所有元素都会根据元素的键值自动排序，set的元素不像map那样可以同时拥有实值(value)和键值(key),set元素的键值就是实值，实值就是键值。set不允许两个元素有相同的键值。

## C++中map的使用

map是一种关联容器，对象的位置取决于和它关联的键的值（键可以是基本类型，也可以是类类型）。

## C++中的优先级队列

在里面是**自动排序**，包含在`<queue>` 中。

```C++
// 普通模式
priority_queue <int> i;
priority_queue <double> d;
// 进阶模式
priority_queue <node> q;
//node是一个结构体
//结构体里重载了‘<’小于符号
priority_queue <int,vector<int>,greater<int> > q;
//不需要#include<vector>头文件
//注意后面两个“>”不要写在一起，“>>”是右移运算符
priority_queue <int,vector<int>,less<int> >q;
```

1. 默认是从大到小排序的
2. 但是可以**重载** < 运算符来判断结构体对象的大小

```C++
bool operator < (const ListNode &a) const{
     return val < a.val;
}
```

* 实现大小根堆

``` C++
// 加入p中的最后会按从大到小排序
priority_queue <int,vector<int>,less<int> > p;
// 加入q中的会从小到大排序
priority_queue <int,vector<int>,greater<int> > q;
```

* 自定义排序方式

```C++
struct cmp{
    bool operator () (const stone &a,const stone& b){
        return a.x < b.x;
    }
};

priority_queue<stone,vector<stone>,cmp> q;
```

## C++中结构体的定义

```c
struct ListNode{

int val;

ListNode *next;

ListNode(int x):val(x),next(NULL){}

};
```

## string的使用

\#include\<string>

```c
string s;//声明一个字符串

cin>>s;

s.length();//返回s的长度

s.substr(int i,int j);//提取字符串返回从i~j的子串
```
