## H5 video 标签

**认识标签属性**

- `playsinline` 浏览器视窗内全屏 isBool
- `poster` 视频封面 isStr

**`object-fit`该样式的做用是指定替换元素的内容应该如何适应到其使用的高度和宽度**

- `fill` 完全填充(会拉伸)
- `contain` 居中、满高定宽(保持宽高比)
- `cover` 居中、满宽定高，超出部分被裁剪(保持宽高比)
- `none` 默认
- `scale-down` 选择`contain`与`none`两者中最小的尺寸

```html
<video
  webkit-playsinline="true"
  playsinline="true"
  poster="test.png"
></video>
```

**完美视口**

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no"/>
```

**Ajax blob流视频**

```js
const video: HTMLVideoElement = document.querySelector('#video');

const xhr = new XMLHttpRequest();

xhr.open('GET', 'https://img.mumiweixin.com/v11.mp4');
xhr.onprogress = function() {
  const loaded: number = e.loaded;
  const target: number = e.loaded;
  if (loaded === target) {
    console.log('100%');
  }
};
xhr.responseType = 'blob'; // 请求的数据类型
xhr.onload = function() {
  var blob = window.URL.createObjectURL(this.response); // 转URL
  video.src = blob;
}
xhr.send();
```