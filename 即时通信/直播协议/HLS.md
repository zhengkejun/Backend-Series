# HLS

HLS, HTTP Live Streaming 是 Apple 提出的直播流协议，其将整个流分成一个个小的块，并基于 HTTP 的文件来下载；HLS 由两部分构成，一个是 .m3u8 文件，一个是 .ts 视频文件；每一个 .m3u8 文件，分别对应若干个 ts 文件，这些 ts 文件才是真正存放视频的数据，m3u8 文件只是存放了一些 ts 文件的配置信息和相关路径，当视频播放时，.m3u8 是动态改变的，video 标签会解析这个文件，并找到对应的 ts 文件来播放，所以一般为了加快速度，.m3u8 放在 web 服务器上，ts 文件放在 CDN 上。HLS 协议视频支持 H.264 格式的编码，支持的音频编码方式是 AAC 编码。

![image](https://user-images.githubusercontent.com/5803001/47569752-fbd91680-d966-11e8-8e5d-491fa49ec18e.png)

.m3u8 文件，其实就是以 UTF-8 编码的 m3u 文件，这个文件本身不能播放，只是存放了播放信息的文本文件：

```
#EXTM3U                 m3u文件头
#EXT-X-MEDIA-SEQUENCE   第一个TS分片的序列号
#EXT-X-TARGETDURATION   每个分片TS的最大的时长
#EXT-X-ALLOW-CACHE      是否允许cache
#EXT-X-ENDLIST          m3u8文件结束符
#EXTINF                 指定每个媒体段(ts)的持续时间（秒），仅对其后面的URI有效
mystream-12.ts
```

HLS 协议的使用也非常便捷，将 m3u8 直接写入到 src 中然后交与浏览器解析，也可以使用 fetch 来手动解析并且获取相关文件：

```js
<video controls autoplay>
  <source
    src="http://devimages.apple.com/iphone/samples/bipbop/masterplaylist.m3u8"
    type="application/vnd.apple.mpegurl"
  />
  <p class="warning">Your browser does not support HTML5 video.</p>
</video>
```

HLS 详细版的内容比上面的简版多了一个 playlist，也可以叫做 master。在 master 中，会根据网络段实现设置好不同的 m3u8 文件，比如，3G/4G/wifi 网速等。比如，一个 master 文件中为：

```
#EXTM3U
#EXT-X-VERSION:6
#EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=2855600,CODECS="avc1.4d001f,mp4a.40.2",RESOLUTION=960x540
live/medium.m3u8
#EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=5605600,CODECS="avc1.640028,mp4a.40.2",RESOLUTION=1280x720
live/high.m3u8
#EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=1755600,CODECS="avc1.42001f,mp4a.40.2",RESOLUTION=640x360
live/low.m3u8
#EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=545600,CODECS="avc1.42001e,mp4a.40.2",RESOLUTION=416x234
live/cellular.m3u8
```

以 high.m3u8 文件为例，其内容会包含：

```
#EXTM3U
#EXT-X-VERSION:6
#EXT-X-TARGETDURATION:10
#EXT-X-MEDIA-SEQUENCE:26
#EXTINF:9.901,
http://media.example.com/wifi/segment26.ts
#EXTINF:9.901,
http://media.example.com/wifi/segment27.ts
#EXTINF:9.501,
http://media.example.com/wifi/segment28.ts
```

该二级 m3u8 文件也可以称为 media 文件，其有三种类型：

- live playlist: 动态列表。顾名思义，该列表是动态变化的，里面的 ts 文件会实时更新，并且过期的 ts 索引会被删除。默认，情况下都是使用动态列表。
- event playlist: 静态列表。它和动态列表主要区别就是，原来的 ts 文件索引不会被删除，该列表是不断更新，而且文件大小会逐渐增大。它会在文件中，直接添加 #EXT-X-PLAYLIST-TYPE:EVENT 作为标识。
- VOD playlist: 全量列表。它就是将所有的 ts 文件都列在 list 当中。如果，使用该列表，就和播放一整个视频没有啥区别了。它是使用 #EXT-X-ENDLIST 表示文件结尾。

显而易见，HLS 的延时包含了 TCP 握手、m3u8 文件下载与解析、ts 文件下载与解析等多个步骤，可以缩短列表的长度和单个 ts 文件的大小来降低延迟，极致来说可以缩减列表长度为 1，并且 ts 的时长为 1s，但是这样会造成请求次数增加，增大服务器压力，当网速慢时回造成更多的缓冲，所以苹果官方推荐的 ts 时长时 10s，所以这样就会大改有 30s 的延迟。
