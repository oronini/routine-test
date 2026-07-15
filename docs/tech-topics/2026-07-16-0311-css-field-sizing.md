# CSS `field-sizing`プロパティ：JavaScriptなしで実現するフォーム要素の自動リサイズ

## 概要

CSSの`field-sizing`プロパティは、`<textarea>`や`<input>`といったフォームコントロールの既定のサイズ決定方式を上書きし、内容量に応じて要素の大きさを自動的に伸縮させるためのプロパティである。`field-sizing: content`を指定するだけで、これまでJavaScriptで`scrollHeight`を監視しながら高さを再計算していたような「自動拡大するテキストエリア」を、CSS一行で実現できるようになった。`field-sizing`は2026年6月16日にBaseline「Newly Available」ステータスに到達し、Chrome・Edge・Operaに加えてSafariでも対応が進んでいる。

## 背景

`<textarea>`や`<input>`はブラウザの既定スタイルにより固定サイズで表示され、内容がその領域を超えるとスクロールバーが表示される仕組みになっている。チャット入力欄やコメントフォームのように、ユーザーの入力量に応じて表示領域を伸縮させたいケースは多く、従来はJavaScriptで`input`イベントごとに`element.style.height`を`scrollHeight`に合わせて再計算する、いわゆる「autosize」系のライブラリやカスタムコードに頼らざるを得なかった。この手法は入力のたびにレイアウト再計算（リフロー）を伴い、パフォーマンスやちらつきの面で課題を抱えていた。`field-sizing`は、この処理をブラウザのレンダリングエンジン内部に委ねることで、JavaScriptに依存しない宣言的な解決策を提供する。

## なぜ重要なのか

`field-sizing`はCSSだけでフォーム要素のサイズを内容に追従させられる点で、実装のシンプルさとパフォーマンスの両面にメリットをもたらす。JavaScriptによる高さ計算処理が不要になることで、入力イベントごとの再描画コストが減り、特に長文入力が想定されるUI（AIチャットボットの入力欄など）で操作の滑らかさが向上する。また、`min-height`や`max-height`と組み合わせることで、一定の行数までは自動拡大し、それを超えたらスクロール表示に切り替えるといった挙動もCSSのみで制御できる。フォーム周りの小さなJavaScript依存を一つ減らせることは、保守性やバンドルサイズの観点からも意味がある。

## 実務での活用例

- チャットアプリやコメント投稿フォームの入力欄に`field-sizing: content`を指定し、`min-height`と`max-height`を併用して、一定行数までは自動拡大・それ以降はスクロールする挙動を実装する。
- 既存のJavaScript製autosizeライブラリ（textarea-autosizeなど）を、対応ブラウザでは`field-sizing`によるCSSのみの実装に置き換え、依存ライブラリを削減する。
- Progressive Enhancementの一環として、`field-sizing`未対応ブラウザ向けにはこれまで通りJavaScriptのフォールバックを残しつつ、対応ブラウザでは自動的にCSSベースの挙動が優先されるよう設計する。
- 検索ボックスや単一行`<input>`にも`field-sizing: content`を適用し、入力文字数に応じて幅が伸縮するUIを実現する。

## 注意点

2026年7月時点でFirefoxは開発中の段階であり、全主要ブラウザでの安定動作を前提にはできないため、本番導入時には`@supports (field-sizing: content)`などによる機能検出とフォールバックの用意が引き続き必要である。Baseline「Widely Available」への到達見込みは2028年12月とされており、当面はJavaScriptによる代替実装との併用や段階的な移行が現実的な選択肢となる。また、`field-sizing: content`は`resize`プロパティによるユーザーの手動リサイズと組み合わせた際の挙動が直感的でない場合があるため、実際のUIでの操作感を事前に検証することが望ましい。

## 参考リンク

- [field-sizing CSS property - MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Properties/field-sizing)
- [field-sizing - Web platform features explorer](https://web-platform-dx.github.io/web-features-explorer/features/field-sizing/)
- [CSS field-sizing — Auto-Resize Form Elements with One Line of CSS | Half Frontend](https://blog.kalan.dev/en/frontend/css-field-sizing/)
- [Auto-Resize Textarea in CSS: field-sizing: content](https://modern-css.com/auto-growing-textarea-without-javascript/)
