---
title: 利用MediaRecorder做直播
date: 2017-12-17 10:28:26
tags:
---

前段时间利用一些时间，学习了直播的内容，前端使用利用MediaRecorder做录屏，后端使用nginx+ffmpeg。
由于MediaRecorder的兼容性问题，这里使用[msr(https://github.com/streamproc/MediaStreamRecorder)做兼容，通过websocket将视频数据发送给后端，利用ffmpeg生成ts文件进行hls直播。

前端通过msr来获取摄像头的图像转成blob对象发送给后端
``` typescript

function recorder() {
    userId = uuidv4();
    socket.emit("start_play", userId);

    navigator.mediaDevices.getUserMedia(mediaSource)
        .then(getStream)
        .catch(console.error);
}
function getStream(stream: MediaStream) {
    devicesStream = stream;
    video.srcObject = stream;

    recorderObj = new MediaStreamRecorder(stream);

    recorderObj.mimeType = recordType;
    recorderObj.ondataavailable = function (blob: any) {
        socket.emit(recordEvent, {
            id: userId,
            data: blob
        });
    };

    recorderObj.start(recordTime);
}
```

后端接收后，通过ffmpeg来生成需要直播的内容
``` typescript
function makeUrl(file) {
    return path.resolve(__dirname, `./cache/${file}.mp4`);
}

function liveFile(data, userId) {
    let id = uuidv4();
    let url = makeUrl(id);
    
    writeFile(url, data, "base64")
    .then(() => {
        live(url, userId);
        deleteFile(url);
    });
}

function live(url, id) {
    ffmpeg(url)
    .duration(30)
    .videoCodec("libx264")
    .format("flv")
    .output(liveUrl(id))
    .run();
}

function deleteFile(url) {
    setTimeout(() => {
        unlink(url)
    }, 120000)
}
```

项目的地址[github](https://github.com/SmallGress/live)

