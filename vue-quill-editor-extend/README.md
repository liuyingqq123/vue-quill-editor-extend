# 基于vue-quill-editor扩展的几个增强组件

功能：


* 提供图片上传到服务器的功能，具体用法请参考[quill-image-extend-module](https://github.com/NextBoy/quill-image-extend-module)
* 提供视频上传到服务器的功能，具体用法请参考[quill-video-extend-module](https://github.com/zhijunzhou/quill-video-extend-module)
* 提供行间距
* 提供字间距
* 提供自定义按钮和事件
* 进行了汉化处理
* 添加了组件的title, 使鼠标移到各个组件上, 有个中文的提示


### 一. 安装
#### 1. 下载vue-quill-editor基础组件

```
npm install vue-quill-editor -S
```

#### 2. 下载image上传图片的扩展组件

```
npm install quill-image-resize-module -S
```


### 二. 扩展组件使用
1. 把src/components/editor文件夹拷贝到你的项目组件库中
2. src/views/demo/LlEditor.vue这个文件就是该扩展组件的用法, 可以基于这个组件在项目里自己二次封装
