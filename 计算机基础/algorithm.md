

[TOC]

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

\#include<stack> 

1. 声明一个栈 stack<int> stk; 
   * stk.push(): 向栈内压入一个成员； 

   * stk.pop(): 从栈顶弹出一个成员； 

   * stk.empty(): 如果栈为空返回true，否则返回false； 

   *  stk.top(): 返回栈顶，但不删除成员； 

   * stk.size(): 返回栈内元素的大小 

## queue的使用 

\#include<queue> 

1. 声明一个对列 queue<int> q; 

   * q.push(x): 将x入队,x进入队尾。 

   *  q.pop(): 队首出队 注意，并不会返回被弹出元素的值。 

   *  q.front(): 访问队首元素，即最早被压入队列的元素。 

   *  q.back():访问队尾元素，即最后被压入队列的元素。 

   *  q.empty():判断队列空，当队列空时，返回true。 

## vector的使用 

相当于在数组之上的封装 

\#include<vector> 

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

## C++中结构体的定义 

```c 
struct ListNode{ 

int val; 

ListNode *next; 

}; 
```

## string的使用 

\#include<string> 

```c 
string s;//声明一个字符串 

cin>>s; 

s.length();//返回s的长度 

s.substr(int i,int j);//提取字符串返回从i~j的子串 
```

# 算法 

## 七大排序算法（冒泡，选择，插入，归并排序，快速排序，堆排序，希尔排序） 

### 冒泡排序

### 选择排序

### 插入排序

### 归并排序

### 快速排序

### 堆排序

### 希尔排序

## 二分查找 

## 海量数据排序 

## topK问题，有1千万个数，怎么快速找出最大的100个 

## 合并两个有序数组，合并两个有序链表 

## 杨氏矩阵（横向递增，纵向递增）中如何找到指定的数字 

## 翻转一句话，例如I am 3 years old，翻转后，old years 3 am I

```c++
class Solution {
public:
  	 // 交换 传引用
     void swap(string &s,int i,int j){
        char a = s[i];
        s[i] = s[j];
        s[j] = a;
    }
    string reverseWords(string s) {
        int len = (int)s.length();
        for (int i = 0,j = 0; j < len; i++) {
            if(s[i]==' '){ //如果是空格的话交换前面的所有
                int now = i+1;
                i--;
                while (j<i) {
                    swap(s,i--,j++);
                }
                i = j = now;
            }
            if(i == len - 1){ //如果是最后一个单词的话
                int now = len;
                while (j<i) {
                    swap(s, i--, j++);
                }
                i = j = now;
            }
        }
        return s;
    }
};
```

## 有10亿条数据，现在只有200M内存，怎么找出这10亿条数据中出现次数最多的100条数据 

