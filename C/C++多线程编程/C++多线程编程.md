# 线程的创建与使用
C++11提供标准方法来创建和使用线程。需要调用头文件```<thread>```。

```cpp
#include <thread>

std::thread t1;

```

## join
在生成并使用线程开始，一直到执行线程的join函数的代码，中间的代码执行和线程的代码执行顺序是无法确定的。
```cpp
std::thread t(F);

... //这一部分代码的执行和线程的代码执行顺序无法确定

t.join();
```

# 锁


# 信号量

C++20提供了标准方法来使用信号量机制。需要调用头文件```<semaphore>```。

