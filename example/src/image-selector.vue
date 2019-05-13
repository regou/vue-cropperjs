<template>
    <div ref="container" class="picture-input" :class="computedClasses">
        <div class="full" v-if="supportsPreview">
            <div class="preview-container"
                 @drag.stop.prevent=""
                 @dragover.stop.prevent=""
                 @dragstart.stop.prevent=""
                 @dragend.stop.prevent=""
                 @dragenter.stop.prevent="onDragEnter"
                 @dragleave.stop.prevent="onDragLeave"
                 @drop.stop.prevent="onFileDrop"
                 @click.prevent="onClick"
                 @keyup.enter="onClick"
            >
                <figure :style="{'background-image': 'url(' + (this.image ? this.image : this.emptyImage) + ')'}"
                     ref="previewImage"
                     class="picture-preview full"
                     tabindex="0"
                ></figure>
                <div v-if="!imageSelected"
                     class="picture-inner">
                    <div class="picture-inner-text">
                        <slot v-if="supportsDragAndDrop" name="support-drop"></slot>
                        <slot v-else name="unsupport-drop"></slot>
                    </div>

                </div>
            </div>
            <!--<button v-if="imageSelected && !hideChangeButton" @click.prevent="selectImage" :class="buttonClass">{{ strings.change }}</button>-->
            <!--<button v-if="imageSelected && removable" @click.prevent="removeImage" :class="removeButtonClass">{{ strings.remove }}</button>-->
            <!--<button v-if="imageSelected && toggleAspectRatio && width !== height" @click.prevent="rotateImage" :class="aspectButtonClass">{{ strings.aspect }}</button>-->
        </div>
        <div v-else class="preview-container" @click.prevent="onClick">
            <slot v-if="imageSelected" name="unsupport-preview-selected">One file Selected</slot>
            <slot v-else name="unsupport-drop"></slot>
        </div>
        <input ref="fileInput" type="file" :name="name" :accept="accept" @change="onFileChange">
    </div>
</template>

<script>
    export default {
        name: 'image-selector',
        props: {
            accept: {
                type: String,
                default: 'image/*'
            },
            size: {
                type: [String, Number],
                default: Number.MAX_SAFE_INTEGER
            },
            name: {
                type: String,
                default: null
            },
            prefill: {
                type: String,
                default: ''
            },
            prefillOptions: {
                type: Object,
                default: () => {
                    return {}
                }
            },
            changeOnClick: {
                type: Boolean,
                default: true
            },
            alertOnError: {
                type: Boolean,
                default: true
            }
        },
        watch: {
            prefill () {
                if (this.prefill) {
                    this.imageSelected = true
                    this.image = this.prefill
                } else {
                    this.removeImage()
                }
            }
        },
        data () {
            return {
                emptyImage: 'data:image/gif;base64,R0lGODlhAQABAIAAAP///wAAACH5BAEAAAAALAAAAAABAAEAAAICRAEAOw==',
                image: undefined,
                imageSelected: false,
                draggingOver: false,
            }
        },
        mounted () {
            if (this.accept !== 'image/*') {
                this.fileTypes = this.accept.split(',')
                this.fileTypes = this.fileTypes.map(s => s.trim())
            }

            this.$on('error', this.onError)
        },
        beforeDestroy () {
            // window.removeEventListener('resize', this.onResize)
            this.$off('error', this.onError)
        },
        methods: {
            onClick () {
                if (!this.imageSelected) {
                    this.selectImage()
                    return
                }

                if (this.changeOnClick) {
                    this.selectImage()
                }

                this.$emit('click')
            },
            onDragEnter () {
                if (!this.supportsDragAndDrop) {
                    return
                }
                this.draggingOver = true
            },
            onDragLeave () {
                if (!this.supportsDragAndDrop) {
                    return
                }
                this.draggingOver = false
            },
            onFileDrop (e) {
                this.onDragLeave()
                this.onFileChange(e)
            },
            onFileChange (e, prefill) {
                if (this.supportsPreview) {
                    let files = e.target.files || e.dataTransfer.files
                    if (!files.length) {
                        return
                    }
                    if (files[0].size <= 0 || files[0].size > this.size * 1024 * 1024) {
                        this.$emit('error', {
                            type: 'fileSize',
                            fileSize: files[0].size,
                            fileType: files[0].type,
                            fileName: files[0].name,
                            message: 'More than (' + this.size + 'MB)'
                        })
                        return
                    }
                    if (files[0].name === this.fileName && files[0].size === this.fileSize && this.fileModified === files[0].lastModified) {
                        return
                    }

                    this.file = files[0]
                    this.fileName = files[0].name
                    this.fileSize = files[0].size
                    this.fileModified = files[0].lastModified
                    this.fileType = files[0].type

                    if (this.accept === 'image/*') {
                        if (files[0].type.substr(0, 6) !== 'image/') {
                            return
                        }
                    } else {
                        if (this.fileTypes.indexOf(files[0].type) === -1) {
                            this.$emit('error', {
                                type: 'fileType',
                                fileSize: files[0].size,
                                fileType: files[0].type,
                                fileName: files[0].name,
                                message: this.strings.fileType
                            })
                            return
                        }
                    }
                    this.imageSelected = true
                    this.image = ''

                    this.loadImage(files[0], prefill || false)
                } else {
                    this.$emit('change', this.$refs.fileInput)
                }
            },
            onError (error) {
                console.error(error)
            },
            loadImage (file, prefill) {
                let reader = new FileReader()
                reader.onload = e => {
                    this.image = e.target.result;
                    if (prefill) {
                        this.$emit('prefill')
                    } else {
                        this.$emit('change', this.image)
                    }
                }
                reader.readAsDataURL(file)
            },
            selectImage () {
                this.$refs.fileInput.click()
            },
            removeImage () {
                this.$refs.fileInput.value = ''
                this.$refs.fileInput.type = ''
                this.$refs.fileInput.type = 'file'
                this.fileName = ''
                this.fileType = ''
                this.fileSize = 0
                this.fileModified = 0
                this.imageSelected = false
                this.image = ''
                this.file = null
                this.imageObject = null
                this.$emit('remove');
                this.$emit('change', undefined)
            }
        },
        computed: {
            supportsPreview () {
                return window.FileReader
            },
            supportsDragAndDrop () {
                const div = document.createElement('div')
                return (('draggable' in div) || ('ondragstart' in div && 'ondrop' in div)) && !('ontouchstart' in window || navigator.msMaxTouchPoints)
            },
            computedClasses () {
                const classObject = {}
                classObject['dragging-over'] = this.draggingOver
                return classObject
            }
        }
    }
</script>

<style scoped>
    .picture-input {
        width: 100%;height: 100%;
        margin: 0 auto;
        text-align: center;
        position: relative;
    }
    .preview-container {
        border: none;
        outline: none;
        width: 100%;
        height: 100%;
        box-sizing: border-box;
        margin: 0 auto;
        cursor: pointer;
    }
    .picture-preview {
        outline: none;
        margin: 0;
        padding: 0;
        background-repeat: no-repeat;
        background-size: contain;
        background-position: 50% 50%;
        z-index: 1;
        box-sizing: border-box;
    }
    .picture-preview:active{
        opacity: 0.9;
    }

    .picture-input.dragging-over .preview-container {
        cursor: copy;
        opacity: 0.5;
    }
    .picture-input.dragging-over .picture-preview{
        filter: brightness(0.5);
    }
    .picture-inner {
        z-index: 2;
        padding: 0.5em;
        pointer-events: none;
        box-sizing: border-box;
        border-radius: 8px;
        display: table;
        width: 100%;height:100%;
    }
    .picture-inner .picture-inner-text {
        display: table-cell;
        vertical-align: middle;
        text-align: center;
    }

    input[type=file] {
        display: none;
    }
    .full{
        position: absolute;
        top:0;bottom:0;left:0;right:0;
    }
</style>
