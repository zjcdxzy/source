title: iOS音频开发学习
date: 2015-12-28 15:05:28
categories: 音视频开发
tags: ffmpeg 
---

## 音频基础

- 声波是一种机械波，是一种模拟信号。
- PCM，全称脉冲编码调制，是一种模拟信号的数字化的方法。
- 采样精度（bit pre sample)，每个声音样本的采样位数。
- 采样频率（sample rate）每秒钟采集多少个声音样本。
- 声道（channel）：相互独立的音频信号数，单声道（mono）立体声（Stereo）
- 语音帧（frame），In audio data a frame is one sample across all channels.

## iOS 开发中对应的结构
###  AudioStreamBasicDescription 结构体
	struct AudioStreamBasicDescription
	{
	    Float64             mSampleRate;
	    AudioFormatID       mFormatID;
	    AudioFormatFlags    mFormatFlags;
	    UInt32              mBytesPerPacket;
	    UInt32              mFramesPerPacket;
	    UInt32              mBytesPerFrame;
	    UInt32              mChannelsPerFrame;
	    UInt32              mBitsPerChannel;
	    UInt32              mReserved;
	};
	
**mSampleRate**：The number of frames per second of the data in the stream.

**mFormatID**:An identifier specifying the general audio data format in the stream. (kAudioFormatLinearPCM, etc.)

**mFormatFlags**:Format-specific flags to specify details of the format.(kLinearPCMFormatFlagIsSignedInteger, kLinearPCMFormatFlagIsFloat, kLinearPCMFormatFlagIsBigEndian, kLinearPCMFormatFlagIsPacked, kLinearPCMFormatFlagIsNonInterleaved, etc.)

**mBytesPerPacket**:The number of bytes in a packet of data
**mFramesPerPacket**:The number of sample frames in each packet of data.
**mBytesPerFrame**:The number of bytes in a single sample frame of data.
**mChannelsPerFrame**:The number of channels in each frame of data
**mBitsPerChannel**:The number of bits of sample data for each channel in a frame of data.

### 数据样例 
	(AudioStreamBasicDescription) _format = {
	  mSampleRate = 44100 
	  mFormatID = kAudioFormatLinearPCM 
	  mFormatFlags = kAudioFormatFlagIsSignedInteger | kAudioFormatFlagIsPacked 
	  mBytesPerPacket = 1 
	  mFramesPerPacket = 1152 
	  mBytesPerFrame = 0 
	  mChannelsPerFrame = 2 
	  mBitsPerChannel = 0 
	  mReserved = 0 
	} 
## iOS音频框架
### 苹果提供的处理音频的系统框架

![](http://7xosar.com1.z0.glb.clouddn.com/core_audio_layers_2x.png)

- 如果需求是播放一段音频（File、Buffer或URL），AVAudioPlayer通常都可以满足需求。
- 如果对性能有要求，或者是播放流式Buffer，那就得用到Audio Queue Services，Audio Queue播放是以Packet形式。
 1. Extended Audio File Services有一些API，可用帮你计算每个包的大小；
 2. Audio File Service或Audio File Stream Services更智能，你只管塞数据给它，它自动把包组好给你。
 3. 在ffmpeg软解播放器中可以使用Auido Queue实现音频播放。










