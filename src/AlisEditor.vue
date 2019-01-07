<template lang="html">
  <div id="ALISEditor">
    <div class="container" id="editor">
    </div>
    <InsertButton
      :editor="editor"
      v-if="insertButton.isVisibleInsertButton"
      :style="{
        left: `${insertButton.posX}px`,
        top: `${insertButton.posY}px`
      }"
      @upload="handleUpload"
    />
  </div>
</template>

<script>
/* eslint-disable no-console */
import Vue from 'vue';
import BalloonEditor from '@ckeditor/ckeditor5-editor-balloon/src/ballooneditor';

import EssentialsPlugin from '@ckeditor/ckeditor5-essentials/src/essentials';
import BoldPlugin from '@ckeditor/ckeditor5-basic-styles/src/bold';
import ItalicPlugin from '@ckeditor/ckeditor5-basic-styles/src/italic';
import LinkPlugin from '@ckeditor/ckeditor5-link/src/link';
import Paragraph from '@ckeditor/ckeditor5-paragraph/src/paragraph';
import ParagraphButtonUI from '@ckeditor/ckeditor5-paragraph/src/paragraphbuttonui';
import Heading from '@ckeditor/ckeditor5-heading/src/heading';
import HeadingButtonsUI from '@ckeditor/ckeditor5-heading/src/headingbuttonsui';
import BlockQuote from '@ckeditor/ckeditor5-block-quote/src/blockquote';
import Image from '@ckeditor/ckeditor5-image/src/image';
import ImageToolbar from '@ckeditor/ckeditor5-image/src/imagetoolbar';
import ImageCaption from '@ckeditor/ckeditor5-image/src/imagecaption';
import ImageStyle from '@ckeditor/ckeditor5-image/src/imagestyle';
import ImageUpload from '@ckeditor/ckeditor5-image/src/imageupload';
import InsertButton from './components/InsertButton';


export default Vue.extend({
  components: {
    // ckeditor: CKEditor.component,
    InsertButton
  },
  data() {
    return {
      insertButton: {
        isVisibleInsertButton: true,
        posX: 0,
        posY: 0,
        target: null
      },
      editor: null,
      initialState: '',
      // editorConfig: {
      //   plugins: [
      //     EssentialsPlugin,
      //     BoldPlugin,
      //     ItalicPlugin,
      //     LinkPlugin,
      //     Heading,
      //     HeadingButtonsUI,
      //     ParagraphButtonUI,
      //     Paragraph,
      //     BlockQuote,
      //     Image,
      //     ImageToolbar,
      //     ImageCaption,
      //     ImageStyle,
      //     ImageUpload
      //   ],
        // image: {
        //   toolbar: ['imageTextAlternative', '|', 'imageStyle:alignLeft', 'imageStyle:full', 'imageStyle:alignRight'],
        //   styles: ['full', 'alignLeft', 'alignRight']
        // },
        // // extraPlugins: [ MyCustomUploadAdapterPlugin ],
        // toolbar: {
        //   items: [
        //     'bold',
        //     'italic',
        //     'link',
        //     'heading1',
        //     'heading2',
        //     'paragraph',
        //     'blockQuote'
        //   ]
        // },
        // heading: {
        //   options: [
        //     { model: 'paragraph', title: 'Paragraph', class: 'ck-heading_paragraph' },
        //     { model: 'heading1', view: 'h1', title: 'Heading 1', class: 'ck-heading_heading1' },
        //     { model: 'heading2', view: 'h2', title: 'Heading 2', class: 'ck-heading_heading2' }
        //   ]
        // }
      // }
    };
  },
  mounted: function() {
    BalloonEditor
      .create( document.querySelector( '#editor' ), {
        plugins: [
          EssentialsPlugin,
          BoldPlugin,
          ItalicPlugin,
          LinkPlugin,
          Heading,
          HeadingButtonsUI,
          ParagraphButtonUI,
          Paragraph,
          BlockQuote,
          Image,
          ImageToolbar,
          ImageCaption,
          ImageStyle,
          ImageUpload
        ],
        toolbar: [
          'bold',
          'italic',
          'link',
          'heading1',
          'heading2',
          'paragraph',
          'blockQuote'
        ],
        image: {
          toolbar: ['imageTextAlternative', '|', 'imageStyle:alignLeft', 'imageStyle:full', 'imageStyle:alignRight'],
          styles: ['full', 'alignLeft', 'alignRight']
        },
        heading: {
          options: [
            { model: 'paragraph', title: 'Paragraph', class: 'ck-heading_paragraph' },
            { model: 'heading1', view: 'h1', title: 'Heading 1', class: 'ck-heading_heading1' },
            { model: 'heading2', view: 'h2', title: 'Heading 2', class: 'ck-heading_heading2' }
          ]
        }
      })
      .then(editor => {
        this.editor = editor
        this.editor.setData('これはテストです <figure class="image"><img src="https://via.placeholder.com/1000x300/02c7cd/fff?text=Placeholder%20image" alt="CKEditor 5 rocks!"></figure>')
        console.log(this.editor)
        this.$nextTick()
        console.log(editor.getData())
        this.initialState = editor.getData()
        console.log(this.initialState)
      })
  },
  methods: {
    handleUpload() {
      console.log('hoge')
    }
  }
});
</script>

<style scoped>
  .container {
    width: 760px;
    margin: 10px auto;
  }
</style>
