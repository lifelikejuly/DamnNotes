# 前言
先简单了解一下音频基本知识

## 声波
声波三要素：频率、振幅、波形。
频率越高，波长越短。

## 数字音频
- 通常所说的音频裸数据格式就是脉冲编码调制（Pulse Code Modulation，PCM）数据。
- PCM数据需要几个概念：量化格式（sampleFormat）、采样率（sampleRate）、声道数（channel）
- 例如CD音质量化格式为16比特（2字节）、采样率44100，声道数为2。比特率计算 44100 * 16 *2 = 1378.125 kbps 1分钟存储大小为 1378.125 * 60 /8 / 1024 = 10.09MB
## 音频编码
- 根据不同应用场景（存储设备、传输网络环境、播放设备等）选用不同压缩编码算法：PCM、WAV、AAC、MP3、Ogg
- 压缩编码原理实际是压缩冗余信号，冗余信号指人耳感知不到听觉范围以外的音频信号以及被屏蔽的音频信号。

# AudioRecord简单使用
- 基本参数配置介绍：audioSource(音频采集输入源）、channelConfig（指定录音器采集几个声道）、audioFormat(采样格式)、bufferSizeInByte(内部音频缓冲区大小，建议开发中使用getMinBufferSize获取可用的bufferSizeInBytes)
- 录音功能可以通过两种方式实例化：构造方法和Build模式。
- 构造方法
```
new AudioRecord(MediaRecorder.AudioSource.MIC,
                    sampleRate, CHANNEL, ENCODING, sizeInBytes)
```
- Build
```
new AudioFormat.Builder())
                    .setChannelMask(getChannelMaskFromLegacyConfig(channelConfig,
                                        true/*allow legacy configurations*/))
                    .setEncoding(audioFormat)
                    .setSampleRate(sampleRateInHz)
                    .build()
```

## Play
开始录音
```
 mAudioRecord.startRecording();
```
结束录音
```
 mAudioRecord.stop();
                mAudioRecord.release();
                mAudioRecord = null;
```
提取数据
可以通过创建一个异步线程不停的从mAudioRecord对象中不断读流数据
```
mAudioRecord.read(mBuffer, 0, mBuffer.length);
```
