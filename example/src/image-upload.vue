<template>
    <div class="ps-image-uploader">
        <image-selector
                name="ps-image-input"
                :prefill="selectedImage"
                ref="pictureInput"
                width="600"
                height="400"
                accept="image/jpeg,image/png"
                size="4"
                @error="onFileError"
                @change="onFileChange">
            <template v-slot:support-drop>
                <icon-camera class="overlayed-camera-icon"></icon-camera>
                <div class="uploader__placeholder_text">
                    <h5>Tap or drag and drop image here</h5>
                    <p>Supports JPG and PNG</p>
                    <p>Maximum file size is 4MB</p>
                </div>

                <!--假按钮，做个样子-->
                <button class="uploader__btn">Upload</button>
            </template>
            <template v-slot:unsupport-drop>
                <icon-camera class="overlayed-camera-icon"></icon-camera>
                <div class="uploader__placeholder_text">
                    <h5>Tap here</h5>
                    <p>Supports JPG and PNG</p>
                    <p>Maximum file size is 4MB</p>
                </div>

                <!--假按钮，做个样子-->
                <button class="uploader__btn">Upload</button>
            </template>
        </image-selector>

        <div v-if="selectedImage" class="ps-image-uploader__edit-button-group">
            <button @click="reselectImage" class="img-edit-button-group-btn"><icon-camera></icon-camera></button>
            <button v-if="selectedImage"
                    @click="isEditing = !isEditing"
                    :class="{active:isEditing}"
                    class="img-edit-button-group-btn"><icon-crop></icon-crop></button>
            <button @click="clearImage" class="img-edit-button-group-btn"><icon-delete></icon-delete></button>
        </div>
        <image-editor @close="stopEditing"
                v-model="selectedImage" v-if="isEditing"></image-editor>
    </div>
</template>

<script>
    import ImageSelector from './image-selector.vue'
    import IconCamera from './svg-icons/icon-camera.vue'
    import IconCrop from './svg-icons/icon-crop.vue'
    import IconDelete from './svg-icons/icon-delete.vue'
    import ImageEditor from './image-editor.vue'

    export default {
        name: "image-upload",
        components: {
            IconCamera,
            IconCrop,
            IconDelete,
            ImageSelector,
            ImageEditor
        },
        data(){
            return {
                selectedImage: undefined,
                isEditing: false
            }
        },
        watch: {
            selectedImage(imageBase64){
                this.$emit('change', {
                    type: 'base64',
                    value: imageBase64
                });
            }
        },
        methods: {
            stopEditing() {
                this.isEditing = false;
            },
            reselectImage() {
                this.$refs.pictureInput.selectImage();
            },
            clearImage() {
                this.$refs.pictureInput.removeImage();
            },
            onFileError(err) {
                this.$emit('fileError', err)
            },
            onFileChange(imageBase64OrDom = undefined){
                if(typeof imageBase64OrDom === 'string'){
                    let imageBase64 = imageBase64OrDom;
                    this.selectedImage = imageBase64;
                    this.isEditing = false;
                    this.$emit('fileChange', imageBase64);
                }else if(imageBase64OrDom){
                    // Preview Unsupported
                    this.$emit('change', {
                        type: 'input',
                        value: imageBase64OrDom
                    });

                }

            }
        }
    }
</script>
<style>
    .img-edit-button-group-btn{
        cursor: pointer;
        padding: 5px 6px;
        border-radius: 3px;
        background-color: #fefefe;
        margin-bottom: 0.5em;
        outline: none;
        border: 1px solid #bbb5b5;
    }

    .img-edit-button-group-btn:active,
    .img-edit-button-group-btn.active{
        border-color: rgba(255,255,255,0);
        background: #f5f5f5;
    }

    .img-edit-button-group-btn svg{
        width: 1.5em;height: 1.5em;
        fill: #505f79;
    }
</style>
<style>
    .ps-image-uploader{
        position: relative;
        border-radius: 3px;
        border: dashed 1px #45a321;
        background-color: #ffffff;
        padding: 0;
    }
    .uploader__placeholder_text h5{
        font-size: 1em;
        margin: 0.5em 0;
        font-weight: 500;
        font-style: normal;
        line-height: 1.71;
        color: #5e6c84;
    }
    .uploader__placeholder_text p{
        font-size: 0.8em;
        font-weight: normal;
        line-height: 1.33;
        margin: 0;
        text-align: center;
        color: #616161;
    }

    .overlayed-camera-icon{
        max-height: 5em; max-width: 5em;
    }
    .overlayed-camera-icon path{
        fill: #f4f5f7;
    }

    .uploader__drop_area input{
        opacity: 0;
        position: absolute;
        width: 5px;height: 5px;
    }


    .uploader__btn{
        background: #FFF;
        border-radius: 3px;
        font-size: 1em;
        padding: 0.2em 1em;
        color: #061632;
        margin: 1em .25em;
        cursor: pointer;
        border: 1px solid #bbb5b5;
        outline: none;
        box-shadow: none;
    }


    .ps-image-uploader__edit-button-group{
        position: absolute;
        top:0;left: calc(100% + 4px);
    }

</style>
