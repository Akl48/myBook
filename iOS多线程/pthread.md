# pthread

### 引入头文件#import<pthread.h> 

1. 创建一个pthread对象 
   1. `pthread_t thread = nil; `

2. 创建线程 
   1. `pthread_creat(&thread,NULL,run,NULL); `
      1. 线程对象，需要传参数 
      2. 线程的一下属性可以穿NULL 

3. 该线程创建后要调用的方法 这里试一个函数指针 
   1. `void * run(void * str);`创建技巧第一个(* )改成函数名 

4. 如果第三个参数（函数指针）中接受的参数没有就NULL 