# CSS Anchor PositioningがBaseline 2026入り、JSツールチップライブラリ不要の時代へ

## 概要

CSS Anchor Positioningは、ある要素（ターゲット）を別の要素（アンカー）の位置を基準にして配置できるようにするCSSの新機能である。ツールチップやポップオーバー、ドロップダウンメニューなど、これまでFloating UIやPopper.jsといったJavaScriptライブラリに頼らざるを得なかったUIパターンを、`anchor()`関数や`position-anchor`プロパティといった純粋なCSSだけで実装できるようになる。2026年にはChrome・Edge・Firefox・SafariのいずれもがCore機能をサポートし、Baseline 2026としてグローバルトラフィックの9割以上をカバーする、実運用可能な段階に達した。

## 背景

これまでツールチップやポップオーバーの位置合わせ（アンカーとターゲットの相対配置、画面端でのはみ出し防止のための自動反転など）は、JavaScriptで要素の座標を計算し続ける必要があり、スクロールやリサイズのたびに再計算が発生するなど実装・パフォーマンス両面でのコストが大きかった。ブラウザベンダーはこの課題に対し、`anchor()`関数でアンカー要素の辺を参照して配置し、`@position-try`で画面内に収まらない場合の代替配置（自動フリップ）をCSS側で宣言的に記述できる仕組みを標準化として進めてきた。2026年時点でChrome 125以降・Firefox 147以降・Safari 26でコア機能がサポートされ、Safari 18.4以降では`@position-try`によるフリップ挙動も含めて動作するようになっている。

## なぜ重要なのか

ポップオーバー系UIの位置計算をブラウザのレンダリングエンジンに委ねられるようになることで、JavaScriptの実行やレイアウト再計算に起因するパフォーマンスコストや、ライブラリ依存によるバンドルサイズの増加を避けられる。また、スクロールやリサイズへの追従もブラウザが自動的に処理するため、これまで開発者が個別に対応していたエッジケース（画面端でのはみ出し、スクロールコンテナ内での追従など）の実装・保守負担が減る。CSS Grid Level 2のsubgridやCustomizable `<select>`（`appearance: base-select`）など、「これまでJavaScriptが担っていたUIロジックをCSS側に引き戻す」という2026年のCSS標準化全体の潮流の中でも、Anchor Positioningは特に影響範囲の広い機能の一つである。

## 実務での活用例

- 既存のFloating UIやPopper.js、あるいは自前実装のツールチップ・ドロップダウンコンポーネントを、対象ブラウザがBaseline 2026要件を満たす場合に`anchor()`・`position-anchor`・`@position-try`を用いたCSSベースの実装へ段階的に置き換える。
- デザインシステムのポップオーバー・ツールチップ・コンボボックスのメニューなど共通コンポーネントにAnchor Positioningを導入し、スクロール追従や画面端での自動反転をCSSのみで実現する。
- `appearance: base-select`によるCustomizable `<select>`と組み合わせ、独自スタイルのドロップダウンをJavaScript製UIライブラリなしで構築する。
- 対象ユーザー層のブラウザバージョン分布を確認し、Safari 18.2〜18.3など`@position-try`のフリップ挙動が未対応な環境向けにフォールバック（固定配置やJSライブラリ併用）を用意する。

## 注意点

- Safari 18.2〜18.3では`anchor()`と`position-anchor`によるコア配置は動作するが、`@position-try`による自動フリップは18.4以降でないと動作しないため、対応バージョンの確認が必要である。
- 古いブラウザや上記の未対応バージョンへの配慮が必要なプロダクトでは、Anchor Positioning単体に一本化せず、JavaScriptライブラリによるフォールバックやプログレッシブエンハンスメントとしての導入を検討すべきである。
- 複雑なレイアウトでは、アンカーとターゲットの対応関係やフォールバックの配置ルール（`@position-try`の優先順位）が増えるほどCSSが煩雑になりやすく、既存のコンポーネント設計との整合性を事前に検証しておく必要がある。

## 参考リンク

- [position-anchor CSS property - MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Properties/position-anchor)
- [anchor() CSS function - MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Values/anchor)
- [CSS Anchor Positioning: Browser Support, Features, Issues | TestMu AI](https://www.testmuai.com/learning-hub/css-anchor-positioning-browser-support/)
- [Customizable select elements - Learn web development | MDN](https://developer.mozilla.org/en-US/docs/Learn_web_development/Extensions/Forms/Customizable_select)
- [The `<select>` element can now be customized with CSS | Chrome for Developers](https://developer.chrome.com/blog/a-customizable-select)
