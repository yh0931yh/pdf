<template>
  <el-row class="home">
    <input type="file" name="myfile" id="myfile" @change="getUrl()"/>
    <el-button @click="preview()">预览上传的文件canvas实现渲染</el-button>
      <pdf-preview
              :dialogVisible="isShowPreview"
              :pdfFileUrl="fileUrl"
              :pdfFileTitle="file.name"
              @closeView="closePreview"

      >
      </pdf-preview>
    <el-row>
      <el-button @click="openLocalUrl()">直接新窗口打开本地路径pdf</el-button>

      <el-button @click="openOutUrl()">直接iframe打开本地路径的pdf</el-button>
      <iframe-preview
              :iframeDialogVisible="isIframeShow"
              @closeIframeView="closeIframeView"
      ></iframe-preview>
    </el-row>

  </el-row>

</template>

<script>
// @ is an alias to /src
import PdfPreview from '@/components/PdfPreview'
import IframePreview from '@/components/IframePreview'
export default {
  name: 'Home',
  components: {
    PdfPreview,
    IframePreview
  },
  data(){
    return{
      isShowPreview:false,
      isIframeShow:false,
      fileUrl:'',
      file:'',
    }
  },
  methods:{
    getUrl(){
      this.file = document.getElementById('myfile').files[0]
      if (this.file.type !== 'application/pdf') {
        alert('只能上传一份pdf格式文件')
        return
      }
    },
    preview() {
      if(this.file){
        this.isShowPreview = true
        this.fileUrl = this.getObjectURL(this.file)
      }else{
        alert('未上传文件')
      }
    },
    getObjectURL(file) {
      let url = null;
      if (window.createObjectURL != undefined) { // basic
        url = window.createObjectURL(file);
      } else if (window.webkitURL != undefined) { // webkit or chrome
        url = window.webkitURL.createObjectURL(file);
      } else if (window.URL != undefined) { // mozilla(firefox)
        url = window.URL.createObjectURL(file);
      }
      return url;
    },
    closePreview(){
      this.isShowPreview=false
    },
    closeIframeView(){
      this.isIframeShow=false
    },
    openLocalUrl(){
      // 此种方式和iframe方式都不接受文件流形式,文件流形式需要对url编码处理 encodeURIComponent(url)
      //编码后还是不识别 url后面加上 &.pdf 试试

      let url='demo.pdf'  //web路径下有demo文件
      window.open('/pdf/web/viewer.html?file=' + url)
    },
    openOutUrl(){
      this.isIframeShow=true
    }
  }
}
</script>
<style>
  .home{
    width: 100%;
    height: 100%;
  }
</style>
