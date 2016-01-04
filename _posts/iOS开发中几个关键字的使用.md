title: iOS开发中几个关键字的使用
date: 2016-01-04 10:39:46
categories: iOS 
tags:
---



## @synchronized 


Objective-C在应用程序中支持多任务，这意味着可能有两个线程同时修改同一个对象，为了防止多个线程同时执行同一个代码块，OC提供了@synchronized（）指令，@synchronized 的作用是创建一个互斥锁，保证此时没有其他线程对对象进行访问。


## @synthesize 和@dynamic之间的区别

在声明property属性后有两种实现选择

## @synthesize
编译器期间，让编译器自动生成getter/setter 方法。
当有自定义的存或取方法时，自定义会屏蔽自动生成该方法

## @dynamic

告诉编译器，不自动生成getter/setter方法，避免编译期间产生警告。
然后由自己实现存取方法。
或存取方法在运行时动态创建绑定：主要使用在CoreData的实现NSManagedObject子类时使用，由Core Data框架在程序运行的时动态生成子类属性


