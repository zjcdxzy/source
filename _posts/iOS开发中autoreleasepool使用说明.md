title: iOS开发中@autoreleasepool使用说明
date: 2015-12-20 17:35:37
categories: iOS
tags:
---



# Document

https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmAutoreleasePools.html

# About Autorelease Pool Blocks

1. 代码块

	``` objective-c
	@autoreleasepool {
	    // Code that creates autoreleased objects.
	}
	```
	
	**At the end of the autorelease pool block, objects that received an autorelease message within the block are sent a release message—an object receives a release message for each time it was sent an autorelease message within the block.**
	
	> 在的autoreleasepool代码块执行结束的时候，自动释放池收到一条autorelease消息，池中的对象收到一条release的消息。
	
2. autorelease的嵌套使用

	``` Objective-C
	@autoreleasepool {
    // . . .
   		 @autoreleasepool {
        	// . . .
    		}
    . . .
	}
	```
	
# Using Autorelease Pool Blocks	
*There are, however, three occasions when you might use your own autorelease pool blocks:*

- If you are writing a program that is not based on a UI framework, such as a command-line tool.

- If you write a loop that creates many temporary objects.

	You may use an autorelease pool block inside the loop to dispose of those objects before the next iteration. Using an autorelease pool block in the loop helps to reduce the maximum memory footprint of the application.

- If you spawn a secondary thread.
You must create your own autorelease pool block as soon as the thread begins executing; otherwise, your application will leak objects

# 什么使用使用@autoreleasepool



1. 写基于命令行的的程序时，就是没有UI框架，如AppKit等Cocoa框架时。(使用的较少)
2. 写循环，循环里面包含了大量临时创建的对象。（使用AudioQueue播放音频读取频繁数据的例子）

	``` 
	static void IJKSDLAudioQueueOuptutCallback(void * inUserData, AudioQueueRef inAQ, AudioQueueBufferRef inBuffer) {
	    @autoreleasepool {
	        IJKSDLAudioQueueController* aqController = (__bridge IJKSDLAudioQueueController *) inUserData;
	
	        if (!aqController) {
	            // do nothing;
	        } else if (aqController->_isPaused || aqController->_isStopped) {
	            memset(inBuffer->mAudioData, aqController.spec.silence, inBuffer->mAudioDataByteSize);
	        } else {
	            (*aqController.spec.callback)(aqController.spec.userdata, inBuffer->mAudioData, inBuffer->mAudioDataByteSize);
	        }
	
	        AudioQueueEnqueueBuffer(inAQ, inBuffer, 0, NULL);
	    }
	}
	```
	**写在@autpreleasepool 代码块中，让每次执行完成后可以及时的释放临时对象的内存**
3. 创建了新的线程。（非Cocoa程序创建线程时才需要）

	```
	static void *SDL_RunThread(void *data)
	{
	    @autoreleasepool {
	        SDL_Thread *thread = data;
	        pthread_setname_np(thread->name);
	        thread->retval = thread->func(thread->data);
	        return NULL;
	    }
	}
	```
	
# 参考文档

-  [@autoreleasepool-内存的分配与释放]( http://tutuge.me/2015/03/17/what-is-autoreleasepool/?utm_source=tuicool&utm_medium=referral)




  

  
	









