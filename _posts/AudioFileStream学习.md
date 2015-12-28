title: AudioFileStream学习
date: 2015-12-28 15:09:28
categories: 音视频开发
tags:
---


## AudioFileStream介绍

- AudioFileStreamer是用来读取采样率、码率、时长等基本信息以及分离音频帧。
- AudioFileStreamer用在流播放中，当然不仅限于网络流，本地文件同样可以用它来读取信息和分离音频帧。
- AudioFileStreamer的主要数据是文件数据，支持的文件格式有：

	* MPEG-1 Audio Layer 3, used for .mp3 files
	* MPEG-2 ADTS, used for the .aac audio data format
	* AIFC
	* AIFF
	* CAF
	* MPEG-4, used for .m4a, .mp4, and .3gp files
	* NeXT
	* WAVE

## AudioFileStreamOpen
初始化AudioFileStream，创建一个音频流解析器，生成一个AudioFileStream示例。

### 函数示例

	extern OSStatus	
	AudioFileStreamOpen (
								void * __nullable						inClientData,
								AudioFileStream_PropertyListenerProc	inPropertyListenerProc,
								AudioFileStream_PacketsProc				inPacketsProc,
	                			AudioFileTypeID							inFileTypeHint,
	                			AudioFileStreamID __nullable * __nonnull outAudioFileStream)
																			__OSX_AVAILABLE_STARTING(__MAC_10_5,__IPHONE_2_0);


### 参数列表：

- inClientData 上下文对象
- AudioFileStream_PropertyListenerProc 歌曲信息解析的回调，每次解析出一个歌曲信息，都会执行一次回调。
- AudioFileStream_PacketsProc 分离帧的回调，每解析出一部分帧就会进行一次回调
- AudioFileTypeID 是文件类型的提示，创建指定文件格式的音频流解析器。AudioFileTypeID对应的枚举

		enum { 
	        kAudioFileAIFFType             = 'AIFF', 
	        kAudioFileAIFCType             = 'AIFC', 
	        kAudioFileWAVEType             = 'WAVE', 
	        kAudioFileSoundDesigner2Type   = 'Sd2f', 
	        kAudioFileNextType             = 'NeXT', 
	        kAudioFileMP3Type              = 'MPG3',    // mpeg layer 3 
	        kAudioFileMP2Type              = 'MPG2',    // mpeg layer 2 
	        kAudioFileMP1Type              = 'MPG1',    // mpeg layer 1 
	        kAudioFileAC3Type              = 'ac-3', 
	        kAudioFileAAC_ADTSType         = 'adts', 
	        kAudioFileMPEG4Type            = 'mp4f', 
	        kAudioFileM4AType              = 'm4af', 
	        kAudioFileM4BType              = 'm4bf', 
	        kAudioFileCAFType              = 'caff', 
	        kAudioFile3GPType              = '3gpp', 
	        kAudioFile3GP2Type             = '3gp2',         
	        kAudioFileAMRType              = 'amrf'         
		}; 
- outAudioFileStream AudioFileStream实例对应的AudioFileStreamID，AudioFileStream其他函数需要使用。

### 返回值

返回值用来判断是否成功初始化（OSSStatus == noErr）

## AudioFileStreamParseBytes 

解析数据，在初始化完成之后，调用该方法解析文件数据。

### 函数示例 
	extern OSStatus AudioFileStreamParseBytes(AudioFileStreamID inAudioFileStream, 
	                                          UInt32 inDataByteSize, 
	                                          const void* inData, 
	                                          UInt32 inFlags); 

### 参数列表
- inAudioFileStream 初始化成功后返回的 AudioFileStreamID
- inDataByteSize 	本次解析的文件长度
- inData				本次解析的数据
- inFlags 标志位，标识本次的解析和上一次解析是否是连续的关系，默认0表示连续，否则传入kAudioFileStreamParseFlag_Discontinuity。

### 返回值
OSStatus的值不是noErr则表示解析不成功，对应的错误码：

	enum 
	{ 
	  kAudioFileStreamError_UnsupportedFileType        = 'typ?', 
	  kAudioFileStreamError_UnsupportedDataFormat      = 'fmt?', 
	  kAudioFileStreamError_UnsupportedProperty        = 'pty?', 
	  kAudioFileStreamError_BadPropertySize            = '!siz', 
	  kAudioFileStreamError_NotOptimized               = 'optm', 
	  kAudioFileStreamError_InvalidPacketOffset        = 'pck?', 
	  kAudioFileStreamError_InvalidFile                = 'dta?', 
	  kAudioFileStreamError_ValueUnknown               = 'unk?', 
	  kAudioFileStreamError_DataUnavailable            = 'more', 
	  kAudioFileStreamError_IllegalOperation           = 'nope', 
	  kAudioFileStreamError_UnspecifiedError           = 'wht?', 
	  kAudioFileStreamError_DiscontinuityCantRecover   = 'dsc!' 
	}; 
每次调用成功后应该注意返回值，一旦出现错误就不必要进行后续的解析。

## AudioFileStream_PropertyListenerProc 

解析文件格式信息的回调，在调用AudioFileStreamParseBytes方法进行解析时会首先读取格式信息，并同步的进入AudioFileStream_PropertyListenerProc回调方法。


### 函数示例
	typedef void (*AudioFileStream_PropertyListenerProc)(
												void *							inClientData,
												AudioFileStreamID				inAudioFileStream,
												AudioFileStreamPropertyID		inPropertyID,
												AudioFileStreamPropertyFlags *	ioFlags);
												
### 参数列表

- **inClientData** 得到上下文
- **inAudioFileStream** 表示当前的FileStream的AudioFileStreamID
- **inPropertyID** 表示此次回调解析的AudioFileStreamPropertyID，

  * 支持的AudioFileStreamPropertyID枚举： 

			enum 
			{ 
			  kAudioFileStreamProperty_ReadyToProducePackets           =    'redy', 
			  kAudioFileStreamProperty_FileFormat                      =    'ffmt', 
			  kAudioFileStreamProperty_DataFormat                      =    'dfmt', 
			  kAudioFileStreamProperty_FormatList                      =    'flst', 
			  kAudioFileStreamProperty_MagicCookieData                 =    'mgic', 
			  kAudioFileStreamProperty_AudioDataByteCount              =    'bcnt', 
			  kAudioFileStreamProperty_AudioDataPacketCount            =    'pcnt', 
			  kAudioFileStreamProperty_MaximumPacketSize               =    'psze', 
			  kAudioFileStreamProperty_DataOffset                      =    'doff', 
			  kAudioFileStreamProperty_ChannelLayout                   =    'cmap', 
			  kAudioFileStreamProperty_PacketToFrame                   =    'pkfr', 
			  kAudioFileStreamProperty_FrameToPacket                   =    'frpk', 
			  kAudioFileStreamProperty_PacketToByte                    =    'pkby', 
			  kAudioFileStreamProperty_ByteToPacket                    =    'bypk', 
			  kAudioFileStreamProperty_PacketTableInfo                 =    'pnfo', 
			  kAudioFileStreamProperty_PacketSizeUpperBound            =    'pkub', 
			  kAudioFileStreamProperty_AverageBytesPerPacket           =    'abpp', 
			  kAudioFileStreamProperty_BitRate                         =    'brat', 
			  kAudioFileStreamProperty_InfoDictionary                  =    'info' 
			}; 

 * 当前PropertyID对应的信息已经解析完成信息，可以通过AudioFileStreamGetProperty接口获取PropertyID对应的值或者数据结构；

			extern OSStatus AudioFileStreamGetProperty(AudioFileStreamID inAudioFileStream, 
	                                           AudioFileStreamPropertyID inPropertyID, 
	                                           UInt32 * ioPropertyDataSize, 
	                                           void * outPropertyData); 

- ioFlags 表示这个property是否需要被缓存。

###  AudioFileStreamPropertyID解析

- **kAudioFileStreamProperty_BitRate** 音频数据的码率，通过码率可以计算音视频的总时长 
- **kAudioFileStreamProperty_DataOffset** 表示音频数据在整个音频文件中的offset 

	*因为大多数音频文件都会有一个文件头之后才使真正的音频数据），这个值在seek时会发挥比较大的作用，音频的seek并不是直接seek文件位置而seek时间（比如seek到2分10秒的位置），seek时会根据时间计算出音频数据的字节offset然后需要再加上音频数据的offset才能得到在文件中的真正offset*
- **kAudioFileStreamProperty_DataFormat** 表示音频文件结构信息，是一个AudioStreamBasicDescription的结构
- **kAudioFileStreamProperty_FormatList** 作用和kAudioFileStreamProperty_DataFormat是一样的，区别在于用这个PropertyID获取到是一个AudioStreamBasicDescription的数组
- **kAudioFileStreamProperty_AudioDataByteCount** 音频文件中音频数据的总量。这个Property的作用一是用来计算音频的总时长，二是可以在seek时用来计算时间对应的字节offset	
- **kAudioFileStreamProperty_ReadyToProducePackets** 一旦回调中这个PropertyID出现就代表解析完成，接下来可以对音频数据进行帧分离了

### 计算时长Duration

 `double duration = (audioDataByteCount * 8) / bitRate`
 
 音频数据的字节总量audioDataByteCount可以通过kAudioFileStreamProperty_AudioDataByteCount获取,
 码率bitRate可以通过kAudioFileStreamProperty_BitRate获取

## AudioFileStream_PacketsProc

分离音频帧回调，读取格式信息完成之后，继续调用AudioFileStreamParseBytes方法可以对帧进行分离，并同步的进入AudioFileStream_PacketsProc回调方法

###  函数示例
	typedef void (*AudioFileStream_PacketsProc)(
												void *							inClientData,
												UInt32							inNumberBytes,
												UInt32							inNumberPackets,
												const void *					inInputData,
												AudioStreamPacketDescription	*inPacketDescriptions);
###  参数列表

- **inClientData** 得到上下文
- **inNumberBytes** 表示本次处理的数据大小
- **inNumberPackets** 本次总共处理了多少帧AudioFileStreamPropertyID 
- inInputData 本次处理的所有数据
- AudioStreamPacketDescription AudioStreamPacketDescription数组，存储了每一帧数据是从第几个字节开始的，这一帧总共多少字节。

## AudioFileStreamSeek
seek操作

## AudioFileStreamClose

AudioFileStream使用完毕后需要调用AudioFileStreamClose进行关闭

### 函数示例
	extern OSStatus AudioFileStreamClose(AudioFileStreamID inAudioFileStream);  
	
参数：inAudioFileStream	初始化时生成的AudioFileStreamID
	
参考资料：

1. [Using Audio](https://developer.apple.com/library/ios/documentation/AudioVideo/Conceptual/MultimediaPG/UsingAudio/UsingAudio.html#//apple_ref/doc/uid/TP40009767-CH2-SW28)
2. [AudioFileStream](http://www.cocoachina.com/industry/20140721/9196.html)

