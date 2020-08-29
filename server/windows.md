# windows

```bat
# 查找端口
netstat -ano|findstr ""
# 查看本机IP
curl -L tool.lu/ip
# 打开远程桌面
mstsc
# 查看系统dns缓存
ipconfig /displaydns
# 清空dns缓存
ipconfig /flushdns
```

### ffmpeg 切割视频

#### 下载

:point_right:[客户端地址](https://ffmpeg.zeranoe.com/builds/)

```shell
ffmpeg -i test.mp4 -codec:v libx264 -codec:a mp3 -map 0 -f ssegment -segment_format mpegts -segment_list playlist.m3u8 -segment_time 10 out%03d.ts
```

-i : 引入视频源

*-codec:v : 视频格式
-codec:a : 音频格式
segment_format: 来指定输出格式为mpegts
segment_list: 用来配置输出的列表文件名
segment_time: 切片的时长*

<font color="red" style="font-weight:bold">下面这条命令貌似快点</font>

```shell
ffmpeg -i .\sxyd.mp4 -c copy -map 0 -f segment -segment_list .\ffpeg-test\video.m3u8 -segment_time 5 .\ffpeg-test\video%d.ts
```

- segment_time 指定切片时间

```shell
# 将 mkv 切成 hls 文件
# 视频可以直接copy，音频要转成 mp3 或 aac，-hls_list_size 默认为 5
ffmpeg -i 1.mkv -c:v copy -c:a mp3 -f hls -hls_time 40 -hls_list_size 99999 out/playlist.m3u8
```

### ffmpeg合并视频

