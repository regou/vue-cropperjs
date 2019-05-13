<template>
    <div class="picture-editor">
        <div style="white-space: nowrap">
            <vue-cropper class="picture-editor_cropper_wrapper"
                    ref="cropper"
                    :guides="true"
                    :view-mode="2"
                    drag-mode="crop"
                    :auto-crop-area="1"
                    :min-container-width="250"
                    :min-container-height="180"
                    :background="true"
                    :rotatable="true"
                    :src="innerValue"
                    alt="Source Image"
                    :img-style="{ 'width': '400px', 'height': '300px' }">
            </vue-cropper>
            <button class="picture-editor_action_btn img-edit-button-group-btn" @click="rotate" v-if="innerValue"><icon-rotate></icon-rotate></button>
        </div>
        <button @click="close">Cancel</button>
        <button @click="cropImage" v-if="innerValue" style="margin-right: 40px;">Submit</button>
    </div>

</template>

<script>
    import IconRotate from './svg-icons/icon-unticlockwise.vue'
    import VueCropper from 'vue-cropperjs';
    export default {
        name: 'image-editor',
        components: {
            VueCropper,
            IconRotate
        },
        props: {
            value: {
                type: String
            }
        },
        computed: {
            innerValue: {
                get() {
                    return this.value;
                },
                set: function(newVal) {
                    this.$emit('input', newVal);
                }
            }
        },
        data() {
            return {
                cropImg: '',
            };
        },
        mounted() {
            this.setImage(this.innerValue);
        },
        methods: {
            close(){
                this.$emit('close');
            },
            setImage(imageBase64) {
                this.$refs.cropper.replace(imageBase64);
            },
            cropImage() {
                this.innerValue = this.$refs.cropper.getCroppedCanvas().toDataURL();
                this.close();
            },
            rotate() {
                this.$refs.cropper.rotate(-90);
            },
        },
    };
</script>

<style>
    .picture-editor{
        position: absolute;
        top: 0; left: calc(100% + 4px + 1em + 16px + 4px);
        border-radius: 3px;
        border: 1px solid #bbb5b5;
        background-color: #fefefe;
        padding: 1em;
        box-shadow: 1px 3px 3px rgba(128, 128, 128, 0.3);
    }

    .picture-editor_cropper_wrapper, .picture-editor_action_btn{
        display: inline-block;
        vertical-align: top;
    }
</style>
