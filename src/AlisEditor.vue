<template lang="html">
  <div id="ALISEditor">
    <div class="container" id="editor" style="border: solid 1px">
    </div>
    <InsertButton
      :articleId="articleId"
      :token="token"
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
import axios from 'axios';
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
import MediaEmbed from '@ckeditor/ckeditor5-media-embed/src/mediaembed';

const IFRAME_SRC = '//cdn.iframe.ly/api/iframe';
const API_KEY = 'xx';

export default Vue.extend({
  props: {
    token: String
  },
  components: {
    InsertButton
  },
  // props: {
  //   articleId: String
  // },
  data() {
    return {
      insertButton: {
        isVisibleInsertButton: false,
        posX: 0,
        posY: 0,
        target: null
      },
      twitterProfileInfo: null,
      title: null,
      description: null,
      dom: null,
      articleId: '',
      editor: null,
      initialState: '',
      meta: null,
      links: null
    };
  },
  mounted() {
    // プラスボタンの挙動制御
    document.addEventListener('selectionchange', event => {
      const selection = window.getSelection()
      const target = selection.anchorNode
      if (target === null) {
        this.insertButton.isVisibleInsertButton = false
        return
      }
      if (target.textContent === "") {
        this.insertButton.isVisibleInsertButton = true
      } else {
        this.insertButton.isVisibleInsertButton = false
      }
    })
    // バルーンエディタ
    BalloonEditor
      .create( document.querySelector('#editor'), {
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
          ImageUpload,
          MediaEmbed
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
        mediaEmbed: {
          previewsInData: false,
          providers: [
            // {
            //   name: 'twitter_profile',
            //   url: 'twitter_profile.alis.to',
            //   html: () => {
            //     if (this.dom !== null) {return (this.dom)}
            //   }
            // },
            {
              name: 'twitter',
              url: /^twitter\.com/,
              html: match => {
                const url = match['input'];
                const urlArr = url.split('/')
                let isTweet = false
                if (urlArr[2] === 'status') {
                  isTweet = true
                }
                const iframeUrl = IFRAME_SRC + '?app=1&api_key=' + API_KEY + '&url=' + encodeURIComponent(url);
                if (isTweet) {
                  return (
                    '<div class="iframely-embed">' +
                    '<div class="iframely-responsive">' +
                    `<iframe src="${ iframeUrl }" ` +
                    'frameborder="0" allow="autoplay; encrypted-media" allowfullscreen">' +
                    '</iframe>' +
                    '</div>' +
                    '</div>'
                  );
                }
                const screenName = urlArr[1];
                return (
                  '<div>' +
                  `<iframe src="http://localhost:3000/media_embed/twitter_profile/${screenName}" +
                  frameborder="0" allow="autoplay; encrypted-media" allowfullscreen +
                  style="width: 100%; height: 160px; left: 0;">` +
                  '</iframe>' +
                  '</div>'
               );
              }
            },
            {
              name: 'facebook',
              url: /^facebook\.com/,
              html: match => {
                const url = 'https://' + match['input'];
                const iframeUrl = IFRAME_SRC + '?app=1&api_key=' + API_KEY + '&url=' + encodeURIComponent(url);
                return (
                  '<div class="iframely-embed">' +
                  '<div class="iframely-responsive">' +
                  `<iframe src="${ iframeUrl }" ` +
                  'frameborder="0" allow="autoplay; encrypted-media" allowfullscreen>' +
                  '</iframe>' +
                  '</div>' +
                  '</div>'
                );
              }
            },
            {
               name: 'youtube',
               url: [
                 /^youtube\.com\/watch\?v=([\w-]+)/,
                 /^youtube\.com\/v\/([\w-]+)/,
                 /^youtube\.com\/embed\/([\w-]+)/,
                 /^youtu\.be\/([\w-]+)/
               ],
               html: match => {
                 const id = match[ 1 ];
                 return (
                   '<div style="position: relative; padding-bottom: 100%; height: 0; padding-bottom: 56.2493%;">' +
                   `<iframe src="https://www.youtube.com/embed/${ id }" ` +
                   'style="position: absolute; width: 100%; height: 100%; top: 0; left: 0;" ' +
                   'frameborder="0" allow="autoplay; encrypted-media" allowfullscreen>' +
                   '</iframe>' +
                   '</div>'
                 );
               }
            },
            {
              name: 'gist',
              url: /^gist\.github\.com/,
              html: match => {
                const url = 'https://' + match['input'];
                const iframeUrl = IFRAME_SRC + '?app=1&api_key=' + API_KEY + '&url=' + encodeURIComponent(url);
                return (
                  '<div class="iframely-embed">' +
                  '<div class="iframely-responsive">' +
                  `<iframe src="${ iframeUrl }" ` +
                  'frameborder="0" allow="autoplay; encrypted-media" allowfullscreen>' +
                  '</iframe>' +
                  '</div>' +
                  '</div>'
                );
              }
            },
            {
              name: 'instagram',
              url: /^www\.instagram\.com\/p\/(\w+)/,
              html: match => {
                const url = 'https://' + match['input'];
                const iframeUrl = IFRAME_SRC + '?app=1&api_key=' + API_KEY + '&url=' + encodeURIComponent(url);
                return (
                  '<div class="iframely-embed">' +
                  '<div class="iframely-responsive">' +
                  `<iframe src="${ iframeUrl }" ` +
                  'frameborder="0" allow="autoplay; encrypted-media" allowfullscreen>' +
                  '</iframe>' +
                  '</div>' +
                  '</div>'
                );
              }
            },
            {
              name: 'any',
              url: /.+/,
              html: match => {
                const url = match[0];
                console.log(match)
                console.log(encodeURIComponent(url))
                // const iframeUrl = IFRAME_SRC + '?app=1&api_key=' + API_KEY + '&url=' + encodeURIComponent(url);
                const data = axios.get(
                  `https://iframe.ly/api/iframely?api_key=${API_KEY}&url=${encodeURIComponent(url)}&omit_script=1&omit_css=1`
                ).then(res => {
                  this.meta = res.data.meta
                  this.links = res.data.links
                })
                console.log(data)
                if (this.meta !== null) {
                  return (
                    '<a href="${url}" target="_blank" class="iframely-embed-card">' +
                      `<div class="title">${this.meta.title || ''}</div>` +
                      `<div class="description">${this.meta.description || ''}</div>` +
                      `<img class="thumbnail" src="${(this.links.thumbnail && this.links.thumbnail[0].href) || (this.links.icon && this.links.icon[0].href)}">` +
                      `<div class="site">あああ</div>` +
                    '</a>'
                  );
                }
                return '<div>hoge</div>'
              }
            }
          ]
        },
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
        // this.editor.setData('')
        // this.$nextTick()
        // this.initialState = editor.getData()
      })
  },
  methods: {
    handleUpload() {
      // console.log('hoge')
    },
    // getTwitterInfo(tweetUserUrl) {
    //   return axios.get(
    //     `https://iframe.ly/api/oembed?api_key=${API_KEY}&url=${encodeURIComponent(
    //     tweetUserUrl
    //     )}&omit_script=1&omit_css=1`
    //   )
    // },
    // setTwitterData(tweetUserUrl) {
    //   this.getTwitterInfo(tweetUserUrl).then((twitterProfileInfo) => {
    //     console.log(twitterProfileInfo)
    //     this.twitterProfileInfo = twitterProfileInfo.data
    //     this.dom =
    //         '<div>' +
    //         `<a href="${tweetUserUrl}" target="_blank" class="twitter-profile-card">` +
    //         `<div class="title">${this.twitterProfileInfo.title}</div>` +
    //         `<div class="description">${this.twitterProfileInfo.description}</div>` +
    //         '<div class="site">twitter.com</div>' +
    //         '</a>' +
    //         '</div>';
    //     this.editor.execute('mediaEmbed', 'twitter_profile.alis.to')
    //     this.dom = null;
    //   })
    // }
  }
});
</script>

<style lang="scss">
.container {
  width: 640px;
  margin: 10px auto;
  font-size: 16px;
}

a {
  color: #030303;
}

.twitter-profile-card {
  border-radius: 4px;
  border: 1px solid #e2e8ed;
  box-sizing: border-box;
  cursor: pointer;
  display: block;
  padding: 20px;
  text-decoration: none;
  width: 100%;
  height: 100%;

  .title {
    color: #030303;
    font-size: 16px;
    font-weight: 500;
    letter-spacing: 0.8px;
    margin-bottom: 8px;
  }

  .description {
    color: #6e6e6e;
    font-size: 14px;
    font-weight: 500;
    letter-spacing: 0.7px;
    line-height: 1.5;
    margin-bottom: 8px;
    overflow: hidden;
    height: 3.6em;
  }

  .site {
    color: #6e6e6e;
    font-size: 12px;
    font-weight: 500;
    letter-spacing: 0.6px;
  }
}

.iframely-embed-card {
  border-radius: 4px;
  border: 1px solid #e3e3e3;
  box-sizing: border-box;
  cursor: pointer;
  display: block;
  height: 140px;
  padding: 20px;
  position: relative;
  text-decoration: none;
  width: 100%;

  .title {
    -webkit-box-orient: vertical;
    -webkit-line-clamp: 1;
    color: #030303;
    display: -webkit-box;
    font-size: 16px;
    font-weight: 500;
    height: 22px;
    letter-spacing: 0.8px;
    line-height: 1.5;
    margin: 0 120px 10px 0;
    overflow: hidden;
    text-align: left;
    text-overflow: ellipsis;
    word-break: break-word;

    &.without-space {
      margin: 0 0 10px 0;
    }
  }

  .description {
    -webkit-box-orient: vertical;
    -webkit-line-clamp: 2;
    color: #6e6e6e;
    display: -webkit-box;
    font-size: 14px;
    font-weight: 500;
    height: 42px;
    letter-spacing: 0.7px;
    line-height: 1.6;
    margin: 0 120px 10px 0;
    overflow: hidden;
    text-align: left;
    text-overflow: ellipsis;
    word-break: break-word;

    &.without-space {
      margin: 0 0 10px 0;
    }
  }

  .thumbnail {
    height: 100px;
    object-fit: cover;
    position: absolute;
    right: 20px;
    top: 20px;
    width: 100px;
  }

  .site {
    color: #6e6e6e;
    font-size: 12px;
    font-weight: 500;
    letter-spacing: 0.6px;
  }
}
</style>
