## webrtc 网络传输---关键帧请求

webrtc会在多个场景中请求I帧，这里仅介绍因"丢包"引起的关键帧请求。

### 待重传的数据包超过上限(1000)

![picture3](../materials/pictures/others_p3.png)

如上图所示，3-1001 号包全都丢失，这个情况下NACK模块会清空所有的待重传序号，并且请求关键帧。

### 不完整的视频帧过多

视频RTP包都会在PacketBuffer中等待组帧，PacketBuffer的长度有限（默认最大值是2048），达到上限时会清空当前的Buffer，并请求关键帧。

### P帧先于第一个I帧先到达

![picture4](../materials/pictures/others_p4.png)

如上图所示，在发送端的顺序是I->P->P，由于网络丢包导致P帧先于I帧达到，会直接触发关键帧请求。

### 长时间没有可解码的视频帧

webrtc接收端会有一个解码线程一直在等待可解码的视频帧，如果"一段时间"内获取不到视频帧就会持续请求关键帧。

"一段时间"是这样定义的:

* 请求关键帧但是200ms内还没有收到关键帧
* 3000ms内收不到普通帧

