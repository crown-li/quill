<template>
  <div class="richText flex x-center">
    <div class="queryForm-content">
      <div id="editor"></div>
      <el-button class="marginBottom10" type="primary" v-dbClick @click="getContent()">提交</el-button>
      <MyImageUpload id="uploadImg" style="margin-left: 20px; width: 98px; height: 40px;" @onChange="onImageChange($event)">
      </MyImageUpload>
    </div>  
  </div>
</template>

<script>

  import MyImageUpload from '@/components/MyImageUpload';
  import { ImageResize } from 'quill-image-resize-module';
  Quill.register('modules/imageResize', ImageResize);
  export default {
    components: {
      MyImageUpload
    },
    data() {
      return {
        
      }
    },
    mounted() {
      this.quill = new Quill('#editor', {
          theme: 'snow',
          debug: 'formats',
          placeholder: '请开始编辑...',
          modules:{
              toolbar: [
                ['bold', 'italic', 'underline', 'strike'],
                ['blockquote', 'code-block'],
                [{ 'header': 1 }, { 'header': 2 }],
                [{ 'list': 'ordered' }, { 'list': 'bullet' }],
                [{ 'script': 'sub' }, { 'script': 'super' }],
                [{ 'indent': '-1' }, { 'indent': '+1' }],
                [{ 'direction': 'rtl' }],
                [{ 'size': ['small', false, 'large', 'huge'] }],
                [{ 'header': [1, 2, 3, 4, 5, 6, false] }],
              //   [{ 'font': [] }],
                [{ 'color': [] }, { 'background': [] }],
                [{ 'align': [] }],
                ['clean'],
                ['code','code-block'],
                ['link', 'image', 'video'],
              ],
              imageResize:{}
          },
        });

        this.resetUploadImg()
     
    },
    methods: {
      
        onImageChange(event) {
            this.imgBase64 = event.base64;
            console.log(event)
            this.$store.dispatch('uploadFile/public',{file: event.file}).then(res => {
              if (res.code === 0) {
                let length = this.quill.getSelection().index;
                // 插入图片  res.info为服务器返回的图片地址
                this.quill.insertEmbed(length, 'image', res.data)
                // 调整光标到最后
                this.quill.setSelection(length + 1)
              }
            })
        },

        getContent() {
            if (this.quill.container.firstChild.innerHTML) {
                this.$copyText(this.quill.container.firstChild.innerHTML).then((e) => {
                this.$Message.success('复制成功');
                }, function(e) {
                this.$Message.error('复制出错');
                })
            }
        },

        resetUploadImg(){
          this.quill.getModule("toolbar").addHandler("image", (value)=>{
            if(value){
              document.querySelector('#uploadImg input').click()
            }else{
              this.quill.format('image', false);
            }
          })
        },

    }
  };
</script>

<style lang="less" scoped>
  .queryForm-content{
      width: 100%;
      #editor{
          min-height: 300px;
      }
  }
</style>

