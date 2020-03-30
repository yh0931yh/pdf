<template>
    <el-dialog
            :title="pdfFileTitle || 'pdf文件预览'"
            :visible.sync="dialogVisible"
            width="60%"
            :before-close="handleClose"
            center
    >
        <div id="pdfCanvas" ref="pdfCanvas" v-loading="isLoading"></div>
    </el-dialog>
</template>

<script>
    import pdf from '../../public/pdf/build/pdf'
    //v3.0 在public 目录下存放pdf js的相关文件;v2.0 在static目录下存放pdf相关文件
    export default {
        name: "PdfPreview",
        data() {
            return {
                isLoading:false
            };
        },
        props:{
            dialogVisible:{type:Boolean,default:false},
            pdfFileUrl:{type:String,default:''},
            pdfFileTitle:{type:String,default:''}
        },
        watch: {
            dialogVisible (val) {
                if (val) {
                    this.$nextTick(()=>{
                        this._loadFile(this.pdfFileUrl)
                    })
                }
            }
        },
        methods: {
            handleClose(){ //关闭弹窗
                this.$emit('closeView')
                document.getElementById('pdfCanvas').innerHTML=""
            },
            _renderPage (pdf,pageNumber,container,numPages) { //渲染pdf
                let _this = this
                pdf.getPage(pageNumber).then((page) => {
                    let scale = (container.offsetWidth/page.view[2])
                    let viewport = page.getViewport(scale)
                    let canvas = document.createElement("canvas")
                    canvas.width= viewport.width
                    canvas.height= viewport.height
                    container.appendChild(canvas)
                    let ctx = canvas.getContext('2d');
                    let renderContext = {
                        canvasContext: ctx,
                        transform: [1, 0, 0, 1, 0, 0],
                        viewport: viewport,
                        intent: 'print'
                    };
                    page.render(renderContext).then(() => {
                        pageNumber +=1
                        if(pageNumber<=numPages) {
                            _this._renderPage(pdf,pageNumber,container,numPages)
                        }else{
                            this.isLoading=false
                        }
                    },err=>{console.log(err)})
                })
            },
            _loadFile (url) {
                // 此种方式接受文件流形式,如不能接受尝试对url编码处理 encodeURIComponent(url)
                //编码后还是不识别 url后面加上 &.pdf 试试
                this.isLoading=true
                this.$refs.pdfCanvas.scrollTop =0
                let pdfjsLib = pdf
                let loadingTask = pdfjsLib.getDocument(url)
                loadingTask.promise.then((pdf) =>{
                    let numPages = pdf.numPages
                    let container = document.getElementById('pdfCanvas')
                    let pageNumber = 1
                    this._renderPage(pdf,pageNumber,container,numPages)
                }, err => {alert(err)});
            },

        }
    }
</script>

<style scoped>
    #pdfCanvas {
        min-height: 30vh;
        max-height: 80vh;
        overflow-y:scroll;
        background: #fff;
        margin: 0 auto;
    }
    canvas{
        display: block;
    }
</style>