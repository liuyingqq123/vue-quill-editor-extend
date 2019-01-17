<template>
  <div class="quill-wrap">
    <quill-editor
      v-model="content"
      ref="myQuillEditor"
      :options="editorOption"
      id="quillId"
      class="editor"
      placeholder="请输入"
    >
    </quill-editor>
    
  </div>
</template>
<script>
  import 'quill/dist/quill.core.css'
  import 'quill/dist/quill.snow.css'
  import 'quill/dist/quill.bubble.css'
  import { quillEditor, Quill } from 'vue-quill-editor'

  import { container, ImageExtend, QuillWatch } from '@/components/editor/imageExtend'
  Quill.register('modules/ImageExtend', ImageExtend)

  import ImageResize from 'quill-image-resize-module'
  Quill.register('modules/ImageResize', ImageResize)

  import { VideoExtend, QuillVideoWatch } from '@/components/editor/videoExtend'
  Quill.register('modules/VideoExtend', VideoExtend)

  let SizeStyle = Quill.import('attributors/style/size');
  SizeStyle.whitelist = ['10px', '12px', '14px', '16px', '18px', '20px', '24px', '30px', '32px', '36px']
  Quill.register(SizeStyle, true);

  let Parchment = Quill.import('parchment')
  const lineHeightList = [
    '1.0',
    '1.2',
    '1.4',
    '1.6',
    '1.8',
    '2.0',
    '2.4',
    '2.6',
    '2.8',
    '3.0',
    '4.0',
    '5.0'
  ]
  const lineHeightConfig = {
    scope: Parchment.Scope.INLINE,
    whitelist: lineHeightList
  }
  const lineHeightClass = new Parchment.Attributor.Class(
    'lineheight',
    'ql-lineheight',
    lineHeightConfig
  )
  const lineHeightStyle = new Parchment.Attributor.Style(
    'lineheight',
    'line-height',
    lineHeightConfig
  )

  Parchment.register(lineHeightClass)
  Parchment.register(lineHeightStyle)

  
  // 字间距
  const letterSpacingList = [
    '0px',
    '1px',
    '2px',
    '3px',
    '4px',
    '5px',
    '6px',
    '7px',
    '8px',
    '9px',
    '10px'
  ]
  const letterSpacingConfig = {
    scope: Parchment.Scope.INLINE,
    whitelist: letterSpacingList
  }
  const letterSpacingClass = new Parchment.Attributor.Class(
    'letterspacing',
    'ql-letterspacing',
    letterSpacingConfig
  )
  const letterSpacingStyle = new Parchment.Attributor.Style(
    'letterspacing',
    'letter-spacing',
    letterSpacingConfig
  )
  Parchment.register(letterSpacingClass)
  Parchment.register(letterSpacingStyle)

  // 按钮title
  const titleConfig = {
    'ql-bold':'加粗',
    'ql-color':'颜色',
    'ql-font':'字体',
    'ql-code':'插入代码',
    'ql-italic':'斜体',
    'ql-link':'添加链接',
    'ql-background':'背景颜色',
    'ql-size':'字体大小',
    'ql-strike':'删除线',
    'ql-script':'上标/下标',
    'ql-underline':'下划线',
    'ql-blockquote':'引用',
    'ql-header':'标题',
    'ql-indent':'缩进',
    'ql-list':'列表',
    'ql-align':'文本对齐',
    'ql-direction':'文本方向',
    'ql-code-block':'代码块',
    'ql-formula':'公式',
    'ql-image':'图片',
    'ql-video':'视频',
    'ql-clean':'清除字体样式',
    'ql-lineheight':'行高',
    'ql-letterspacing':'字间距',
    'ql-preview':'预览'
  }

  export default {
    components: { quillEditor },
    data() {
      return {
        // defaultOptions,
        content: '',
        // 富文本框参数设置
        editorOption: {
          placeholder: '请输入内容',
          modules: {
            VideoExtend: {
              loading: true,
              name: 'video',
              action: '',
              headers: (xhr) => {
                // set custom token(optional)
                xhr.setRequestHeader('token', this.token)
              },
              response: (res) => {
                // video uploaded path
                // custom your own
                // return process.env.BASE_API + res.data.path
                return res.data.path
              }
            },
  
            ImageExtend: {
              loading: true,
              name: 'img',
              size: 2, // 单位为M, 1M = 1024KB
              action: '',
              headers: (xhr) => {
              },
              response: (res) => {
                return res.info
              }
            },
            ImageResize: {
              parchment: Quill.import('parchment'),
              modules: ['Resize', 'DisplaySize', 'Toolbar']
            },
            //  toolbar: '#toolbar',
            toolbar: {
              container: container,
              // container: Object.assign(container, {'size': [false, '10px', '12px', '14px', '16px', '18px', '20px', '24px', '30px', '32px', '36px']}),
              handlers: {
                'image': function() {
                  console.log(this.quill.id)
                  QuillWatch.emit(this.quill.id)
                },
                'video': function() {
                  console.log(this.quill.id)
                  QuillVideoWatch.emit(this.quill.id)
                },
                shadeBox:null,
                sourceEditor: function(){     //添加预览工具
                  alert("我要预览")
                }
              }
            },
          }
        }
      }
    },
    mounted() {
      let vm = this;
      vm.initButton();
      vm.addQuillTitle();
    },
    methods: {
      initButton:function(){      //在使用的页面中初始化按钮样式
        const sourceEditorButton = document.querySelector('.ql-preview');
        sourceEditorButton.style.cssText = "width:60px;";
        sourceEditorButton.innerText="预览";
      },
      addQuillTitle(){ // 按钮添加title
        const oToolBar = document.querySelector('.ql-toolbar'),
              aButton = oToolBar.querySelectorAll('button'),
              aSelect =  oToolBar.querySelectorAll('select');
        aButton.forEach(function(item){
          if(item.className === 'ql-script'){
            item.value === 'sub' ? item.title = '下标': item.title = '上标';
          }else if(item.className === 'ql-indent'){
            item.value === '+1' ? item.title ='向右缩进': item.title ='向左缩进';
          }else{
            item.title = titleConfig[item.classList[0]];
          }
        });
        aSelect.forEach(function(item){
          item.parentNode.title = titleConfig[item.classList[0]];
        });
      },
    }
  }
</script>

<style lang="scss">

// 字号修改为px
.ql-picker-item[data-value='10px']::before, .ql-picker-label[data-value='10px']::before {
  content: '10px' !important;
}

.ql-picker-item[data-value='12px']::before, .ql-picker-label[data-value='12px']::before {
  content: '12px' !important;
}

.ql-picker-item[data-value='14px']::before, .ql-picker-label[data-value='14px']::before {
  content: '14px' !important;
}

.ql-picker-item[data-value='16px']::before, .ql-picker-label[data-value='16px']::before {
  content: '16px' !important;
}

.ql-picker-item[data-value='18px']::before, .ql-picker-label[data-value='18px']::before {
  content: '18px' !important;
}

.ql-picker-item[data-value='20px']::before, .ql-picker-label[data-value='20px']::before {
  content: '20px' !important;
}

.ql-picker-item[data-value='24px']::before, .ql-picker-label[data-value='24px']::before {
  content: '24px' !important;
}

.ql-picker-item[data-value='30px']::before, .ql-picker-label[data-value='30px']::before {
  content: '30px' !important;
}

.ql-picker-item[data-value='32px']::before, .ql-picker-label[data-value='32px']::before {
  content: '32px' !important;
}

.ql-picker-item[data-value='36px']::before, .ql-picker-label[data-value='36px']::before {
  content: '36px' !important;
}

// 行间距
.ql-snow .ql-picker.ql-lineheight {
  width: 58px;
}

.ql-snow .ql-picker.ql-lineheight .ql-picker-label::before,
.ql-snow .ql-picker.ql-lineheight .ql-picker-item::before {
  content: '行间距';
}
.ql-snow .ql-picker.ql-lineheight .ql-picker-item[data-value='1.0']::before {
  content: '1.0';
}
.ql-snow .ql-picker.ql-lineheight .ql-picker-label[data-value='1.0']::before {
  content: '1.0' !important;
}
.ql-snow .ql-picker.ql-lineheight .ql-picker-item[data-value='1.2']::before {
  content: '1.2';
}
.ql-snow .ql-picker.ql-lineheight .ql-picker-label[data-value='1.2']::before {
  content: '1.2' !important;
}
.ql-snow .ql-picker.ql-lineheight .ql-picker-item[data-value='1.5']::before {
  content: '1.5';
}
.ql-snow .ql-picker.ql-lineheight .ql-picker-label[data-value='1.5']::before {
  content: '1.5' !important;
}
.ql-snow .ql-picker.ql-lineheight .ql-picker-item[data-value='1.6']::before {
  content: '1.6';
}
.ql-snow .ql-picker.ql-lineheight .ql-picker-label[data-value='1.6']::before {
  content: '1.6' !important;
}
.ql-snow .ql-picker.ql-lineheight .ql-picker-item[data-value='1.8']::before {
  content: '1.8';
}
.ql-snow .ql-picker.ql-lineheight .ql-picker-label[data-value='1.8']::before {
  content: '1.8' !important;
}
.ql-snow .ql-picker.ql-lineheight .ql-picker-item[data-value='2.0']::before {
  content: '2.0';
}
.ql-snow .ql-picker.ql-lineheight .ql-picker-label[data-value='2.0']::before {
  content: '2.0' !important;
}
.ql-snow .ql-picker.ql-lineheight .ql-picker-item[data-value='2.4']::before {
  content: '2.4';
}
.ql-snow .ql-picker.ql-lineheight .ql-picker-label[data-value='2.4']::before {
  content: '2.4' !important;
}
.ql-snow .ql-picker.ql-lineheight .ql-picker-item[data-value='2.8']::before {
  content: '2.8';
}
.ql-snow .ql-picker.ql-lineheight .ql-picker-label[data-value='2.8']::before {
  content: '2.8' !important;
}
.ql-snow .ql-picker.ql-lineheight .ql-picker-item[data-value='3.0']::before {
  content: '3.0';
}
.ql-snow .ql-picker.ql-lineheight .ql-picker-label[data-value='3.0']::before {
  content: '3.0' !important;
}
.ql-snow .ql-picker.ql-lineheight .ql-picker-item[data-value='4.0']::before {
  content: '4.0';
}
.ql-snow .ql-picker.ql-lineheight .ql-picker-label[data-value='4.0']::before {
  content: '4.0' !important;
}
.ql-snow .ql-picker.ql-lineheight .ql-picker-item[data-value='5.0']::before {
  content: '5.0';
}
.ql-snow .ql-picker.ql-lineheight .ql-picker-label[data-value='5.0']::before {
  content: '5.0' !important;
}

// 字间距
.ql-snow .ql-picker.ql-letterspacing {
  width: 70px;
}

.ql-snow .ql-picker.ql-letterspacing .ql-picker-label::before,
.ql-snow .ql-picker.ql-letterspacing .ql-picker-item::before {
  content: '字间距';
}
.ql-snow .ql-picker.ql-letterspacing .ql-picker-item[data-value='0px']::before {
  content: '0px';
}
.ql-snow .ql-picker.ql-letterspacing .ql-picker-label[data-value='0px']::before {
  content: '0px' !important;
}
.ql-snow .ql-picker.ql-letterspacing .ql-picker-item[data-value='1px']::before {
  content: '1px';
}
.ql-snow .ql-picker.ql-letterspacing .ql-picker-label[data-value='1px']::before {
  content: '1px' !important;
}
.ql-snow .ql-picker.ql-letterspacing .ql-picker-item[data-value='2px']::before {
  content: '2px';
}
.ql-snow .ql-picker.ql-letterspacing .ql-picker-label[data-value='2px']::before {
  content: '2px' !important;
}
.ql-snow .ql-picker.ql-letterspacing .ql-picker-item[data-value='3px']::before {
  content: '3px';
}
.ql-snow .ql-picker.ql-letterspacing .ql-picker-label[data-value='3px']::before {
  content: '3px' !important;
}
.ql-snow .ql-picker.ql-letterspacing .ql-picker-item[data-value='4px']::before {
  content: '4px';
}
.ql-snow .ql-picker.ql-letterspacing .ql-picker-label[data-value='4px']::before {
  content: '4px' !important;
}
.ql-snow .ql-picker.ql-letterspacing .ql-picker-item[data-value='5px']::before {
  content: '5px';
}
.ql-snow .ql-picker.ql-letterspacing .ql-picker-label[data-value='5px']::before {
  content: '5px' !important;
}
.ql-snow .ql-picker.ql-letterspacing .ql-picker-item[data-value='6px']::before {
  content: '6px';
}
.ql-snow .ql-picker.ql-letterspacing .ql-picker-label[data-value='6px']::before {
  content: '6px' !important;
}
.ql-snow .ql-picker.ql-letterspacing .ql-picker-item[data-value='7px']::before {
  content: '7px';
}
.ql-snow .ql-picker.ql-letterspacing .ql-picker-label[data-value='7px']::before {
  content: '7px' !important;
}
.ql-snow .ql-picker.ql-letterspacing .ql-picker-item[data-value='8px']::before {
  content: '8px';
}
.ql-snow .ql-picker.ql-letterspacing .ql-picker-label[data-value='8px']::before {
  content: '8px' !important;
}
.ql-snow .ql-picker.ql-letterspacing .ql-picker-item[data-value='9px']::before {
  content: '9px';
}
.ql-snow .ql-picker.ql-letterspacing .ql-picker-label[data-value='9px']::before {
  content: '9px' !important;
}
.ql-snow .ql-picker.ql-letterspacing .ql-picker-item[data-value='10px']::before {
  content: '10px';
}
.ql-snow .ql-picker.ql-letterspacing .ql-picker-label[data-value='10px']::before {
  content: '10px' !important;
}

// 汉化
.editor {
  line-height: normal !important;
  height: 800px;
}
.ql-snow .ql-tooltip[data-mode=link]::before {
  content: "请输入链接地址:";
}
.ql-snow .ql-tooltip.ql-editing a.ql-action::after {
    border-right: 0px;
    content: '保存';
    padding-right: 0px;
}

.ql-snow .ql-tooltip[data-mode=video]::before {
    content: "请输入视频地址:";
}

.ql-snow .ql-picker.ql-size .ql-picker-label::before,
.ql-snow .ql-picker.ql-size .ql-picker-item::before {
  content: '14px';
}
.ql-snow .ql-picker.ql-size .ql-picker-label[data-value=small]::before,
.ql-snow .ql-picker.ql-size .ql-picker-item[data-value=small]::before {
  content: '10px';
}
.ql-snow .ql-picker.ql-size .ql-picker-label[data-value=large]::before,
.ql-snow .ql-picker.ql-size .ql-picker-item[data-value=large]::before {
  content: '18px';
}
.ql-snow .ql-picker.ql-size .ql-picker-label[data-value=huge]::before,
.ql-snow .ql-picker.ql-size .ql-picker-item[data-value=huge]::before {
  content: '32px';
}

.ql-snow .ql-picker.ql-header .ql-picker-label::before,
.ql-snow .ql-picker.ql-header .ql-picker-item::before {
  content: '标题';
}
.ql-snow .ql-picker.ql-header .ql-picker-label[data-value="1"]::before,
.ql-snow .ql-picker.ql-header .ql-picker-item[data-value="1"]::before {
  content: '标题1';
}
.ql-snow .ql-picker.ql-header .ql-picker-label[data-value="2"]::before,
.ql-snow .ql-picker.ql-header .ql-picker-item[data-value="2"]::before {
  content: '标题2';
}
.ql-snow .ql-picker.ql-header .ql-picker-label[data-value="3"]::before,
.ql-snow .ql-picker.ql-header .ql-picker-item[data-value="3"]::before {
  content: '标题3';
}
.ql-snow .ql-picker.ql-header .ql-picker-label[data-value="4"]::before,
.ql-snow .ql-picker.ql-header .ql-picker-item[data-value="4"]::before {
  content: '标题4';
}
.ql-snow .ql-picker.ql-header .ql-picker-label[data-value="5"]::before,
.ql-snow .ql-picker.ql-header .ql-picker-item[data-value="5"]::before {
  content: '标题5';
}
.ql-snow .ql-picker.ql-header .ql-picker-label[data-value="6"]::before,
.ql-snow .ql-picker.ql-header .ql-picker-item[data-value="6"]::before {
  content: '标题6';
}

.ql-snow .ql-picker.ql-font .ql-picker-label::before,
.ql-snow .ql-picker.ql-font .ql-picker-item::before {
  content: '标准字体';
}
.ql-snow .ql-picker.ql-font .ql-picker-label[data-value=serif]::before,
.ql-snow .ql-picker.ql-font .ql-picker-item[data-value=serif]::before {
  content: '衬线字体';
}
.ql-snow .ql-picker.ql-font .ql-picker-label[data-value=monospace]::before,
.ql-snow .ql-picker.ql-font .ql-picker-item[data-value=monospace]::before {
  content: '等宽字体';
}

// 覆盖美化
.ql-toolbar.ql-snow .ql-formats {
  margin-bottom: 3px;
  margin-top: 3px;
}
.ql-snow .ql-picker.ql-size .ql-picker-label::before,
.ql-snow .ql-picker.ql-size .ql-picker-item::before {
  content: '大小';
}
.ql-snow .ql-picker.ql-size {
  width: 60px;
}
.ql-snow .ql-picker.ql-header {
  width: 65px;
}
.ql-snow .ql-picker.ql-font {
  width: 90px;
}
.ql-snow .ql-picker.ql-lineheight {
  width: 75px;
}
.ql-snow .ql-picker.ql-letterspacing {
  width: 75px;
}
.ql-toolbar.ql-snow {
  background: #f8f8f8;
}
</style>