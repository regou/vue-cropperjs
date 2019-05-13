<template>
  <div id="app">
    <image-upload @change="onChange"></image-upload>
  </div>
</template>

<script>
  import ImageUpload from './image-upload.vue';


  export default {
    components: {
      ImageUpload,
    },
    data() {
      return {

      };
    },
    methods: {
        onChange(data){
            console.log(data);
            if(data.type === 'input'){
                // 不支持编辑和预览，要post form
                alert(1);
            }
        }
    },
  };
</script>

<style>
  .ps-image-uploader{
    width: 15rem;height: 15rem;
  }
</style>
