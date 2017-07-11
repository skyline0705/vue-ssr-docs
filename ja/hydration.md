# クライアントサイドでのハイドレーション

`entry-client.js` において、以下の記述で私たちは簡単にアプリケーションをマウントします。

```js
// これは、ルート要素に id="app" をもつ App.vue テンプレートを想定します。
app.$mount('#app')
```

サーバがマークアップを描画後に、この処理を実行し、すべての DOM を再生成することを私たちは当然したくありません。代わりに、静的なマークアップの"ハイドレート"とそれをインタラクティブに生成したいです。

もしあなたがサーバレンダリングの出力を調べたら、アプリケーションのルート要素が以下のような特別な属性を持っていることに気づくでしょう。

```js
<div id="app" data-server-rendered="true">
```

この `data-server-rendered` という特別な属性は、クライアントサイドの Vue に、これがサーバ上で描画されたことを知らせ、この要素はハイドレーションモードでマウントされるはずです。

開発モードでは、Vue はクラインアントサイドで生成された仮想 DOM が、サーバで描画された DOM の構成とマッチしているか検証を行います。もしこれがマッチしていない場合、ハイドレーションを取りやめ、元の DOM を無視しスクラッチから描画を行います。**プロダクションモードでは、パフォーマンスの最大化のため、このアサーションは無効になります。**

### ハイドレーション時の注意

サーバサイドレンダリングとクライアントサイドでのハイドレーションを行なった場合、ある特定の HTML の構造はブラウザによって変換されるかもしれないことがわかっています。例えば、あなたが Vueのテンプレート内に、以下のような記述をした場合です。

```html
<table>
  <tr><td>hi</td></tr>
</table>
```

ブラウザは、自動で `<tbody>` を `<table>` に挿入します。しかし、Vue によって生成された仮想 DOM は、`<tbody>` を含みません。そのため、ミスマッチが起こります。正しいマッチングを保証するために、あなたのテンプレート内では、必ず有効な HTML を記述してください。