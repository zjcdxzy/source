title: 静态库支持cpu构架查看方式
date: 2015-12-28 10:04:44
categories: Tips
tags:
---

## 使用lipo -info 命令

	localhost:leCloudPlayerSDK zjc$ lipo -info libLeClouldLivingPlayer.a 
	Architectures in the fat file: libLeClouldLivingPlayer.a are: armv7 i386 x86_64 arm64 

## 使用file命令

	localhost:leCloudPlayerSDK zjc$ file libLeClouldLivingPlayer.a 
	libLeClouldLivingPlayer.a: Mach-O universal binary with 4 architectures
	libLeClouldLivingPlayer.a (for architecture armv7):	current ar archive random library
	libLeClouldLivingPlayer.a (for architecture i386):	current ar archive random library
	libLeClouldLivingPlayer.a (for architecture x86_64):	current ar archive random library
	libLeClouldLivingPlayer.a (for architecture arm64):	current ar archive random library
