<template lang="html">
  <div id="ALISEditor" class="container">
    <ckeditor :editor="editor" v-model="editorData" :config="editorConfig"></ckeditor>
  </div>
</template>

<script>
/* eslint-disable no-console */
import Vue from 'vue';
import CKEditor from '@ckeditor/ckeditor5-vue';
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

export default Vue.extend({
  components: {
    ckeditor: CKEditor.component
  },
  data() {
    return {
      editor: BalloonEditor,
      editorData: 'これはテストです',
      editorConfig: {
        plugins: [
          EssentialsPlugin,
          BoldPlugin,
          ItalicPlugin,
          LinkPlugin,
          Heading,
          HeadingButtonsUI,
          ParagraphButtonUI,
          Paragraph,
          BlockQuote
        ],
        toolbar: {
          items: [
            'bold',
            'italic',
            'link',
            'heading1',
            'heading2',
            'paragraph',
            'blockQuote'
          ]
        },
        heading: {
          options: [
            { model: 'paragraph', title: 'Paragraph', class: 'ck-heading_paragraph' },
            { model: 'heading1', view: 'h1', title: 'Heading 1', class: 'ck-heading_heading1' },
            { model: 'heading2', view: 'h2', title: 'Heading 2', class: 'ck-heading_heading2' }
          ]
        }
      }
    };
  },
  mounted: function() {
    // BalloonEditor
    //     .create(document.querySelector('#app'))
    console.log(this.editor)
  }
});
</script>

<style scoped>
  .container {
    width: 760px;
    margin: 10px auto;
  }
</style>
