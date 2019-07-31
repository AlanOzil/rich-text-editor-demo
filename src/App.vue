<template>
<div id="app">
  <editor id="editor" v-model="tinymceHtml" :init="editorInit"></editor>
</div>
</template>

<script>
import tinymce from 'tinymce/tinymce'
import 'tinymce/themes/silver'
import 'tinymce/skins/ui/oxide/skin.min.css'
import Editor from '@tinymce/tinymce-vue'
// 载入插件
import 'tinymce/plugins/image'
import 'tinymce/plugins/imagetools'
import 'tinymce/plugins/link'
import 'tinymce/plugins/paste'
import 'tinymce/plugins/fullscreen'
import 'tinymce/plugins/preview'
const __IMG_UPLOAD_URL__ = 'https://sm.ms/api/upload'
export default {
  name: 'app',
  data() {
    return {
      tinymceHtml: '',
      editorInit: {
        language_url: `/tinymce/langs/zh_CN.js`,
        language: 'zh_CN',
        height: 300,
        branding: false, // 去水印
        elementpath: false, //禁用编辑器底部的状态栏
        statusbar: false, // 隐藏编辑器底部的状态栏
        paste_data_images: true, // 允许粘贴图像
        plugins: 'image imagetools link paste fullscreen preview',
        images_upload_url: __IMG_UPLOAD_URL__,
        // override default upload handler to simulate successful upload
        images_upload_handler: function(blobInfo, success, failure) {
          var xhr, formData
          xhr = new XMLHttpRequest()
          xhr.withCredentials = false
          xhr.open('POST', __IMG_UPLOAD_URL__)

          xhr.onload = function() {
            var json
            if (xhr.status != 200) {
              failure('HTTP Error: ' + xhr.status)
              alert('图片上传失败')
              return
            }
            json = JSON.parse(xhr.responseText)
            if (!json || !json.data || typeof json.data.url != 'string') {
              failure('Invalid JSON: ' + xhr.responseText)
              alert('图片上传失败')
              return
            }
            success(json.data.url) //用服务器地址替换img的src
          }
          formData = new FormData()
          formData.set('smfile', blobInfo.blob())
          xhr.send(formData)
        }
      }
    }
  },
  components: {
    Editor
  },
  mounted() {
    tinymce.init({
      ...this.editorInit,
      selector: '#editor',
      theme: 'silver',
      skin: "oxide",
      skin_url: '/tinymce/skins/ui/oxide'
    })
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
