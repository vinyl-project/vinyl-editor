<template>
    <div id="container" ref="container" style="width:100%;height:100%"></div>
</template>


<script>
import * as monaco from 'monaco-editor/esm/vs/editor/edcore.main'
export default {
  props: {
    codes: {
      type: String,
      default: function () {
        return ''
      }
    },
    language: {
      type: String,
      default: function () {
        return 'python'
      }
    },
    editorOptions: {
      type: Object,
      default: function () {
        return {
          selectOnLineNumbers: true,
          roundedSelection: false,
          readOnly: false, // 只读
          cursorStyle: 'line', // 光标样式
          automaticLayout: false, // 自动布局
          glyphMargin: true, // 字形边缘
          useTabStops: false,
          fontSize: 28, // 字体大小
          autoIndent: true// 自动布局
          // quickSuggestionsDelay: 500,   //代码提示延时
        }
      }
    }
  },
  data () {
    return {
      themeOption: [
        {
          value: 'vs',
          label: '默认'
        },
        {
          value: 'hc-black',
          label: '高亮'
        },
        {
          value: 'vs-dark',
          label: '深色'
        }
      ],
      theme: 'vs-dark',
      codesCopy: null// 内容备份
    }
  },
  mounted () {
    this.initEditor()
  },
  methods: {
    initEditor () {
      let self = this
      self.$refs.container.innerHTML = ''
      self.monacoEditor = monaco.editor.create(self.$refs.container, {
        value: self.codesCopy || self.codes,
        language: self.language,
        theme: self.theme, // vs, hc-black, or vs-dark
        editorOptions: self.editorOptions
      })
      self.$emit('onMounted', self.monacoEditor)// 编辑器创建完成回调
      self.monacoEditor.onDidChangeModelContent(function (event) { // 编辑器内容changge事件
        self.codesCopy = self.monacoEditor.getValue()
        self.$emit('onCodeChange', self.monacoEditor.getValue(), event)
      })
      // 编辑器随窗口自适应
      window.addEventListener('resize', function () {
        if (self.monacoEditor) {
          self.monacoEditor.layout()
        }
      })
    },
    RunResult () {
      console.log(this.monacoEditor.getValue())
    },
    themeChange (val) {
      this.initEditor()
    }
  }
}
</script>
