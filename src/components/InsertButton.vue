/* eslint-disable */
<template>
  <div class="insert-button">
    <div
      class="insert-button__toggle"
      @click="toggleIsOpen"
      :class="{
        'is-open': isOpen
      }"
    >
      <span>+</span>
    </div>
    <ul class="insert-button__list" v-if="isOpen">
      <li class="insert-button__listItem" @click="dispatchUpload">●</li>
      <!--<li class="insert-button__listItem" @click="appendRule"><InsertButtonIcon :src="SvgIcon.rule" /></li>-->
    </ul>
    <input type="file" @change="onFileChange">
  </div>
</template>

<script>
    /* eslint-disable no-console */
import Vue from 'vue'
import axios from 'axios'
import InsertImage from '../InsertImage'

export default Vue.extend({
  props: {
    editor: Object,
    articleId: String,
    token: String
  },
  data() {
    return {
      isOpen: false,
      doc: null,
      uploadFile: null,
      uploadImageSize: null,
      uploadImageExtension: null
    }
  },
  methods: {
    toggleIsOpen() {
      this.isOpen = !this.isOpen
      console.log(this.editor)
      this.doc = this.editor.model.document
    },
    dispatchUpload() {
      this.$el.querySelector('[type="file"]').click()
    },
    // appendRule() {
    //   this.$emit('append', BlockType.Rule)
    // },
    handleUpload() {
      this.isOpen = false;
      // this.$emit('disable');
      // this.$emit('upload', event)
      console.log(event)
    },
    onFileChange(event) {
      this.uploadFile = event.target.files[0]
      if (this.uploadFile === null) {
        return
      }
      console.log(this.uploadFile)
      console.log(this.token)
      this.uploadImageSize = this.uploadFile.size
      this.uploadImageExtension = this.uploadFile.type
      const config = {
        headers: {
          'Authorization': this.token,
          'Access-Control-Allow-Origin': '*',
          'Content-Type': 'application/json'
        },
        params: {
          'upload_image_size': this.uploadImageSize,
          'upload_image_extension': this.uploadImageExtension
        }
      }
      axios.get(`https://xxxx/api/me/articles/${this.articleId}/image_upload_url`, config)
        .then((res) => {
          console.log(res)
          const json = JSON.parse(JSON.stringify(res.data))
          console.log(json)
          axios.put(json['url'], this.uploadFile, {
            headers: {
              'Content-Type': this.uploadFile.type
            }
          }).then((res) => {
            console.log('fu')
            console.log(res)
            InsertImage(this.editor, `https://xxx/${json['upload_url_suffix']}`, '', this.doc)
          })
        })
    }
  }
})
</script>

<style scoped>
.insert-button {
  position: absolute;
  left: -100px;
  top: -100px;
  margin: 8px;
  cursor: pointer;
  z-index: 1000000000000000000;
  display: flex;
}

.insert-button .insert-button__toggle {
  width: 30px;
  height: 30px;
  display: flex;
  align-items: center;
  justify-content: center;
  border: solid 1px #ddd;
  color: #ddd;
  border-radius: 50%;
  font-weight: bold;
  padding-left: 1px;
  padding-bottom: 1px;
  transition: all 0.05s linear;
  transform: rotate(0deg);
  font-size: 25px;
  font-weight: normal;
  font-family: 'Yu Gothic', YuGothic;
  box-shadow: 0 0 10px 0 hsla(0, 0%, 57%, 0.5);
}

.insert-button__toggle span {
  pointer-events: event;
  user-select: none;
}

.insert-button .insert-button__toggle.is-open {
  transform: rotate(45deg);
}

.insert-button .insert-button__list {
  margin: 0;
  padding: 0;
  color: #fff;
  overflow: hidden;
  display: flex;
  list-style-type: none;
  font-size: 1.4rem;
}

.insert-button__list .insert-button__listItem {
  margin-left: 8px;
  background: #fff;
  width: 30px;
  height: 30px;
  display: flex;
  align-items: center;
  justify-content: center;
  border: solid 1px #ddd;
  color: #ddd;
  border-radius: 50%;
  font-weight: bold;
  padding-left: 1px;
  padding-bottom: 1px;
  transition: all 0.05s linear;
  transform: rotate(0deg);
  font-size: 25px;
  font-weight: normal;
  font-family: 'Yu Gothic', YuGothic;
  box-shadow: 0 0 10px 0 hsla(0, 0%, 57%, 0.5);
}

.insert-button__list .insert-button__listItem:hover {
  background: #f9f9f9;
}

.insert-button__list .insert-button__listItem + .insert-button__listItem {
  border-left: solid 1px #fff;
}

input {
  display: none;
}
</style>
