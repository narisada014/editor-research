# editor-research

yarn serve でエラー確認できます

# 本家のIssue
https://github.com/ckeditor/ckeditor5-vue/issues

# 公式ドキュメント内の問題な箇所
https://ckeditor.com/docs/ckeditor5/latest/builds/guides/integration/frameworks/vuejs.html#using-ckeditor-from-source

公式ドキュメントではvue.config.jsの中身を
```
    chainWebpack: config => {
        // Vue CLI would normally use its own loader to load .svg files. The icons used by
        // CKEditor should be loaded using raw-loader instead.
        config.module
            .rule( 'svg' )
            .test( /ckeditor5-[^/\\]+[/\\]theme[/\\]icons[/\\][^/\\]+\.svg$/ )
            .use( 'file-loader' )
            .loader( 'raw-loader' );
    }
```
こう書けとあるがraw-loaderでエラーが起きる
