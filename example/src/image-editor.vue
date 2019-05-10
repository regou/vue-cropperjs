<template>
    <div class="picture-editor">
        <vue-cropper
                ref="cropper"
                :guides="true"
                :view-mode="2"
                drag-mode="crop"
                :auto-crop-area="1"
                :min-container-width="250"
                :min-container-height="180"
                :background="true"
                :rotatable="true"
                :src="imgSrc"
                alt="Source Image"
                :img-style="{ 'width': '400px', 'height': '300px' }">
        </vue-cropper>
        <img :src="cropImg" style="width: 200px; height: 150px; border: 1px solid gray" alt="Cropped Image" />

        <button @click="cropImage" v-if="imgSrc != ''" style="margin-right: 40px;">Crop</button>
        <button @click="rotate" v-if="imgSrc != ''">Rotate</button>
    </div>

</template>

<script>
    import VueCropper from 'vue-cropperjs';
    export default {
        name: 'image-editor',
        components: {
            VueCropper,
        },
        props: {
            imgSrc: {
                type: String
            },
        },
        data() {
            return {
                cropImg: '',
            };
        },
        created() {
            console.log('setImage', this.imgSrc);
            this.setImage(this.imgSrc);
        },
        methods: {
            setImage(imageBase64) {
                this.$refs.cropper.replace(imageBase64);
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
