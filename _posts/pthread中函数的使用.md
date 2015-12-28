title: pthread中函数的使用
date: 2015-12-21 09:52:50
categories: iOS
tags:
---

pthread中函数的使用

# 互斥锁

在编程中，引入了对象互斥锁的概念，来保证共享数据操作的完整性。每个对象都对应于一个可称为" 互斥锁" 的标记，**这个标记用来保证在任一时刻，只能有一个线程访问该对象**。

## pthread_mutex_init
该函数用于C函数的多线程编程中，互斥锁的初始化。

* pthread_mutex_init()函数是以动态方式创建互斥锁的。
* pthread_mutexattr_init()函数成功完成之后会返回零，其他任何返回值都表示出现了错误。
* 函数成功执行后，互斥锁被初始化为未锁住态。	

## pthread_mutex_lock

pthread_mutex_lock() 返回时，改互斥锁已经别锁定。线程调用该函数让互斥锁上锁，如果该互斥锁已经被另一个线程锁定和拥有，则调用线程将阻塞，直到该互斥锁变为可用为止。

## pthread_mutex_unlock

与pthread_mutex_lock成对存在。释放互斥锁


## pthread_mutex_destroy

互斥锁销毁函数在执行成功后返回 0，否则返回错误码。


# 条件变量 

Linux下C编程的条件变量：

* 条件变量是线程中的东西，就是等待某一条件的发生，和信号一样。
* 条件变量使我们可以睡眠等待某种条件出现。
* 条件变量是利用线程间共享的全局变量进行同步的一种机制，主要包括两个动作：一个线程等待"条件变量的条件成立"而挂起；另一个线程使"条件成立"（给出条件成立信号）。**为了防止竞争，条件变量的使用总是和一个互斥锁结合在一起。**

## pthread_cond_init
函数pthread_cond_init（）被用来初始化一个条件变量。

* 默认条件变量被同一进程内的各个线程使用。
* 初始化条件变量只有未被使用时才能重新初始化或被释放。 
* 函数成功返回0；任何其他返回值都表示错误。

## pthread_cond_wait

等待条件有两种方式：条件等待pthread_cond_wait()和计时等待pthread_cond_timedwait()，其中计时等待方式如果在给定时刻前条件没有满足，则返回ETIMEOUT，结束等待.

无论哪种等待方式，都必须和一个互斥锁配合，以防止多个线程同时请求pthread_cond_wait()的竞争条件。在调用pthread_cond_wait()前必须由本线程加锁（pthread_mutex_lock()），mutex保持锁定状态，在线程挂起进入等待前解锁。

**pthread_cond_wait必须放在pthread_mutex_lock和pthread_mutex_unlock之间，因为他要根据共享变量的状态来决定是否要等待，而为了不永远等待下去所以必须要在lock/unlock队中**


## pthread_cond_signal
pthread_cond_signal函数的作用是发送一个信号给另外一个正在处于阻塞等待状态的线程,使其脱离阻塞状态,继续执行.如果没有线程处在阻塞等待状态,pthread_cond_signal也会成功返回。

**thread_cond_signal建议放在pthread_mutex_lock和pthread_mutex_unlock之间**

## pthread_cond_destroy
注销一个条件变量需要调用pthread_cond_destroy()，只有在没有线程在该条件变量上等待的时候，才能注销这个条件变量，否则返回失败
