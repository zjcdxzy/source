title: iOS应用程序在后台保持运行实现方式
date: 2015-12-26 20:52:06
categories: iOS
tags:
---

## 在iOS系统上应用程序在后台如何长时间运行
	
1. 播放音乐
2. 声明Audio
3. 获取GPS信号

## iOS 长时间后台的两种方法

iOS开启后台任务后只有600s的执行时间， 大约10分钟，现在需要实现在后台保持下载，或者需要在应用进入后台时仍然和服务器保持连接。

## 后台播放一个静音的音频 

* 就是在后台一直播放一个无声的音乐文件，可以轻松的突破600s的限制
* app进入后台后audiosession不能被打断，如果另一个程序使用KAudioSessionCategory_RecordAndPlay 或者 kAudioSessionCategory_SoloAmbientSound 那么本程序就会被立即打断。
* 播放被打断后可以处理一个通知:audiosessioninterruption。在收到这个通知后可以做相应的处理，然后程序会立即被转入到休眠状态，被系统自动挂起，appdelegateDidEnterBackground回调函数会自动调用。
* 被打断后建议直接停止下载服务，不需要再等待音频播放服务重启。

## VoipSocket 

VOIPSocket 可以在后台运行，当程序进入后台时，默认应用程序会别系统挂起，但是VOIPSocket因为受系统控制所以不在此列。可以在启动一个voipsocket，这样可以保证应用程序在后台运行。
	



