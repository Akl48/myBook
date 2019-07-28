[toc] 

# block 

## _ NSConcreteStackBlock 

特点:存放在栈区,生命周期由系统控制的，一旦返回之后，就被系统销毁了。 

栈block的表现形式: 

1. 拥有局部变量(自动变量)或者成员属性变量(即使被strong或者copy修饰的成员变量) 

2. 没有被强引用 通过__weak实现 

## _ NSConcreteGlobalBlock 

特点:这种Block存储在程序的数据区域（跟函数存储在一起),生命周期从创建到应用程序结束。全局block的copy是个空操作,实际上就相当于单例. 

全局block的表现形式: 

1. block中没有用到任何block内部以外的变量 

2. block内部仅仅用到了全局变量/静态全局变量,静态变量 

## _ NSConcreteMallocBlock 

特点:存放在堆区,没有强指针引用即销毁，生命周期由程序员控制 

堆block的表现形式: 

1. 堆中的block无法直接创建，其需要由_NSConcreteStackBlock类型的block拷贝而来(也就是说block需要执行copy之后才能存放到堆中) 

2. 在ARC环境下，以下几种情况,编译器会自动的判断，把Block自动的从栈copy到堆 

手动调用copy的栈block： 

1. 栈Block被强引用，被赋值给__strong或者id类型 

2. copy修饰的成员属性引用的block会被复制一份到堆中成为MallocBlock,这可以归纳为上一条 

3. Block是函数的返回值 

4. 调用系统API入参中含有usingBlcok的方法 

