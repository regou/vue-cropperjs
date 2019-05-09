<template>
  <div id="app">
    <image-upload></image-upload>
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
        imgSrc: '',
        cropImg: '',
      };
    },
    methods: {
      setImage(e) {
        const file = e.target.files[0];
        if (!(file.type.indexOf('image/') >= 0)) {
          alert('Please select an image file');
          return;
        }

        if (typeof FileReader === 'function') {
          const reader = new FileReader();

          reader.onload = (event) => {
            this.imgSrc = event.target.result;
            // rebuild cropperjs with the updated source
            this.$refs.cropper.replace(event.target.result);
          };

          reader.readAsDataURL(file);
        } else {
          alert('Sorry, FileReader API not supported');
        }
      },
      cropImage() {
        // get image data for post processing, e.g. upload or setting image src
        this.cropImg = this.$refs.cropper.getCroppedCanvas().toDataURL();
      },
      rotate() {
        // guess what this does :)
        this.$refs.cropper.rotate(90);
      },
    },
  };
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
  .ps-image-uploader{
    width: 15em;
  }
</style>
