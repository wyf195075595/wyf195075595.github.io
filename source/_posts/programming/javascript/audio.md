---
title: Audio API
date: 2022-06-17 08:23:10
tags: js
categories: js
---

> HTML的<audio>和<video>标签可以让我们在网页中轻松包含音频和视频。这两个元素有着重要的API和并不简单的用户界面。可以通过play()和pause()方法控制媒体播放。可以设置volume和playbackRate属性控制音量和播放速度。而设置currentTime属性可以跳到媒体中特定的时间点。

<!--more-->

```js
1、Audio 构造函数 创建 audio 元素

let soundeffect = new Audio('soundeffect.mp3');
// 用户点击时播放
document.addEventListener('click', ()=>{
    soundeffect.cloneNode().play();
})
	注意这里使用了cloneNode()。如果用户快速单击鼠标，我们希望同时播放多个重叠的音效。为此，就需要有多个Audio元素。因为这些Audio元素并未添加到文档中，所以它们播放结束后就会被当作垃圾清理掉。
    
    
    
2、除了使用Audio元素播放录制的声音，浏览器也可以通过WebAudio API生成和播放合成音效。使用WebAudio API就像是使用带接续柱的老式电子合成器。对于WebAudio，要创建一组AudioNode对象，表示波形的来源、变换和目标，然后再将这些节点连接为一个网络以产生声音。这个API并不很复杂，但要全面解释还需要理解电子音乐和信号处理的概念，这些都超出了本书的范畴。

eg: 一秒钟的短和弦
// 首先创建一个 audioContext 对象， Safari 热然要求使用
// webkitAudioContext 而不是 AudioContext

let audioContext = new (this.AudioContext || this.webkitAudioContext)();

// 定义基准声音要为三个正弦波的组合
let notes = [293.7, 370.0, 440.0]; // D三大和弦：D、F# 和 A

// 为每个想要播放的音符创建振荡器节点
let oscillators = notes.map(note => {
    let o = audioContext.createOscillator();
    o.frequency.value = note;
    return o ;
})

// 通过随时间控制音量来构造声音
// 从时间0开始快速升为最大音量
// 然后从时间0.1开始缓慢降为0

let volumeControl = audioContext.createGain();
volumeControl.gain.setTargetAtTime(1, 0.0, 0.02);
volumeControl.gain.setTargetAtTime(0, 0.1, 0.2);

// 我们想把这个声音发送给默认目标
// 用户的扬声器
let speakers = audioContext.destination;

// 把每个源音字符都连接到音量控制
oscillators.forEach(o => o.connect(volumeControl));

// 再把音量控制的输出连接到扬声器
volumeControl.connect(speakers);

// 现在开始播放声音，让他们持续1.25s
let startTime = audioContext.currentTime;
let stopTime = startTime + 1.25;
oscillators.forEach(o => {
    o.start(startTime);
    o.stop(stopTime)
})

// 如果想创建一系列声音，可以使用事件处理程序
oscillators[0].addEventListener('ended', ()=> {
    // 在音符停止播放时会调用这个事件处理程序
})
```

### 实现语音输入

要将 Blob 对象转换为音频文件，您可以使用 Web API 中的 URL.createObjectURL() 方法来创建 Blob URL，然后将其赋值给音频元素的 src 属性，最后将音频文件下载到本地。

获取音频可以使用 [mic-recorder-to-mp3](https://unpkg.com/browse/mic-recorder-to-mp3@2.2.2/)

```js
// 获取 Blob 对象
const blob = new Blob([data], { type: 'audio/wav' });

// 创建 Blob URL
const url = URL.createObjectURL(blob);

// 创建音频元素
const audio = new Audio(url);

// 播放音频
audio.play();

// 下载音频文件到本地
const a = document.createElement('a');
a.href = url;
a.download = 'audio.wav';
document.body.appendChild(a);
a.click();
document.body.removeChild(a);

// 释放 Blob URL
URL.revokeObjectURL(url);
```

> 请注意，在使用完 Blob URL 后，应该调用 URL.revokeObjectURL() 方法来释放资源，避免内存泄漏。

[代码](https://codepen.io/wyf195075595/pen/xxMyXKQ?editors=1111)

```react
navigator.mediaDevices.getUserMedia({ audio: true }).then(
  () => {
    console.log("准备好了");
  },
  () => {
    console.log("准备失败");
  }
);
class App extends React.Component {
  render() {
    let recordStart = null;
    const Mp3Recorder = new MicRecorder({
      bitRate: 128
    });
    const start = () => {
      Mp3Recorder.start().then(() => {
        recordStart = Date.now();
      });
    };
    const end = () => {
      Mp3Recorder.stop()
        .getMp3()
        .then(([buffer, blob]) => {
          let voice = document.getElementById("voice");
          // 获取 Blob 对象
          const blob1 = new Blob([blob], { type: "audio/wav" });
          // 创建 Blob URL
          const url = URL.createObjectURL(blob1);
          voice.src = url;
          
        })
        .then(() => {
          console.log("失败");
        });
    };
    return (
      <div>
        <h1>hello world</h1>
        <audio id="voice" controls src="" />
        <button onMouseDown={start} onMouseUp={end}>
          按住
        </button>
      </div>
    );
  }
}

ReactDOM.render(<App />, document.getElementById("app"));

```

