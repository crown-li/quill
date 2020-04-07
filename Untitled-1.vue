<template>
  <div class="richText flex x-center">
    <div class="queryForm-content">
      <!-- <div id="editor"></div> -->
      <quillEditor
          v-model="detailContent"
          ref="quillEditor"
          :options="editorOption"
          @change="onEditorChange($event)"
          @ready="onEditorReady($event)"
        >
      </quillEditor>
      <el-button class="marginBottom10" type="primary" v-dbClick @click="getContent()">提交</el-button>
      <MyImageUpload id="uploadImg" style="margin-left: 20px; width: 98px; height: 40px;" @onChange="onImageChange($event)">
      </MyImageUpload>
    </div>  
  </div>
</template>

<script>
  import { quillEditor } from 'vue-quill-editor'
  import 'quill/dist/quill.snow.css'
  import MyImageUpload from '@/components/MyImageUpload';
  export default {
    components: {
      quillEditor,
      MyImageUpload
    },
    data() {
      return {
        editorOption: {
            placeholder: '请开始编辑...',
            theme: 'snow',  
            modules: {
                toolbar: {
                  container: [
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
                  ],  // 工具栏
                  handlers: {
                      'image': function (value) {
                          if (value) {
                              document.querySelector('#uploadImg input').click()
                          } else {
                              this.quill.format('image', false);
                          }
                      }
                  },
                },
                ImageResize:{}
            }
        },
        detailContent:''
      }
    },
    computed: {
      quill() {
        return this.$refs.quillEditor.quill
      }
    },
    mounted() {
      console.log('this.quill:',this.quill)
      // this.quill.register('modules/imageResize', ImageResize);
      //this.quill.getModule("toolbar").addHandler("image", (value)=>{

    },
    methods: {

      onEditorChange(){
        
      },

      onEditorReady(){

      },
      
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
          if (this.detailContent) {
              this.$copyText(this.detailContent).then((e) => {
              this.$Message.success('复制成功');
              }, function(e) {
              this.$Message.error('复制出错');
              })
          }
      },

      // resetUploadImg(){
      //   this.quill.getModule("toolbar").addHandler("image", (value)=>{
      //     if(value){
      //       document.querySelector('#uploadImg input').click()
      //     }else{
      //       this.quill.format('image', false);
      //     }
      //   })
      // },

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

