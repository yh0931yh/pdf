<template>
    <el-dialog
            title="iframe预览pdf"
            :visible.sync="iframeDialogVisible"
            width="80%"
            :before-close="iframeHandleClose"
            center
    >
        <iframe v-if="iframeDialogVisible" id='previewPdf' :src="'/pdfjs-dist/web/viewer.html?file=' + pdfFileUrl" height="560"
                width="100%">
        </iframe>
    </el-dialog>
</template>

<script>
    export default {
        name: "IframePreview",
        props:{
            iframeDialogVisible:{type:Boolean,default:false},
            pdfFileUrl:{type:String,default:"demo.pdf"},
        },
        data(){
            return{
            }
        },
        methods:{
            iframeHandleClose(){ //关闭弹窗
                this.$emit('closeIframeView')
            },
        }
    }
</script>

<style scoped>

</style>