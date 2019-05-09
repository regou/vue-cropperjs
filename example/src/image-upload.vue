<template>
    <div class="ps-image-uploader">


        <image-selector
                ref="pictureInput"
                width="600"
                height="400"
                accept="image/jpeg,image/png"
                size="10"
                :custom-strings="{
        upload: '<h1>Bummer!</h1>',
        drag: 'Drag a 😺 GIF or GTFO'
      }"
                @change="onChange">
            <template v-slot:support>
                <label class="uploader__drop_area">
                    <input class="box__file" type="file" name="file" data-multiple-caption="{count} files selected" />
                    <svg class="camera-icon" viewBox="0 0 24 24">
                        <g fill="none" fill-rule="evenodd">
                            <path d="M18.4 6.6c.88 0 1.6.72 1.6 1.6v9.6c0 .88-.72 1.6-1.6 1.6H5.6c-.88 0-1.6-.72-1.6-1.6V8.2c0-.88.72-1.6 1.6-1.6h2.536l.984-1.08c.304-.328.736-.52 1.184-.52h3.392c.448 0 .88.192 1.176.52l.992 1.08H18.4zM12 17c2.208 0 4-1.792 4-4s-1.792-4-4-4-4 1.792-4 4 1.792 4 4 4zm0-1.6a2.4 2.4 0 1 1 0-4.8 2.4 2.4 0 0 1 0 4.8z"/>
                        </g>
                    </svg>
                    <div class="uploader__placeholder_text">
                        <h5>Tap or drag and drop image here</h5>
                        <p>Supports JPG and PNG</p>
                        <p>Maximum file size is 4MB</p>
                    </div>

                    <button class="uploader__btn">Upload</button>
                </label>
            </template>
        </image-selector>
    </div>
</template>

<script>
    import ImageSelector from './image-selector.vue'
    export default {
        name: "image-upload",
        components: {
            ImageSelector,
        },
        methods: {
            onChange(){

            }
        }
    }
</script>

<style scoped>
    .ps-image-uploader{
        border-radius: 3px;
        border: dashed 1px #45a321;
        background-color: #ffffff;
        padding: 1em;
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

    .uploader__drop_area {
        display: block;
    }
    .uploader__drop_area input{
        opacity: 0;
        position: absolute;
        width: 5px;height: 5px;
    }

    .camera-icon{
        width: 25%;
    }
    .camera-icon path{
        fill: #f4f5f7;
    }
    .uploader__btn{
        background: #FFF;
        border-radius: 3px;
        font-size: 1em;
        margin-top: 1rem;
        padding: 0.2em 1em;
        color: #061632;
    }

</style>
