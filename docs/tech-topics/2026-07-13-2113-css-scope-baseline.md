# CSS `@scope`がBaseline対応に：主要ブラウザ全てでスコープ付きスタイルが利用可能に

## 概要

CSSのスタイルスコープを宣言的に制御できる`@scope`ルールが、Firefox 146での実装により主要ブラウザすべてに揃った。Chrome・Edge・Opera・Samsung Internetは118以降、Safariは17.4以降ですでに対応しており、今回のFirefox対応によって「Baseline: Newly Available」の扱いとなった。`@scope`は、DOMのサブツリーに対してスタイルの適用範囲を上限・下限セレクタで明示的に区切れる仕組みであり、過度に詳細なセレクタを書かずにコンポーネント単位のスタイル分離を実現できる。

## 背景

CSSは本質的にグローバルなカスケードを持つ言語であり、大規模なプロジェクトでは、あるコンポーネント用のスタイルが意図せず別の要素に影響してしまう「スタイル漏れ」が長年の課題だった。これに対しては、BEMのような命名規則、CSS Modules、CSS-in-JS、Shadow DOMなど様々な手法で対処されてきたが、いずれもビルドツールの導入や独自の記法学習を必要とするものが多かった。`@scope`はこの課題に対するネイティブCSSの解決策として仕様化され、`@scope (<上限セレクタ>) to (<下限セレクタ>) { ... }`という構文で、特定のDOMサブツリー内だけにスタイルを限定できる。下限セレクタで「ここから先は対象外」という除外（いわゆるdonut scope）も表現できる点が、単純な子孫セレクタとの大きな違いである。

## なぜ重要なのか

`@scope`がBaseline Newly Availableになったことで、追加のビルドステップやポリフィルなしに、CSSだけでコンポーネント単位のスタイル境界を作れるようになった。特にデザインシステムやコンポーネントライブラリの開発において、ネストした子コンポーネントのスタイルが親のスタイルと衝突する問題を、ビルドツール非依存の標準機能で回避できる意義は大きい。また、`@scope`内では距離ベースの詳細度計算が行われるため、スコープの近さによって自然に優先順位が決まり、`!important`の乱用や過剰に長いセレクタチェーンを減らす効果も期待できる。Web Componentsを使わずとも、素のHTML/CSSでスタイルカプセル化に近い体験が得られる点も、既存資産を持つプロジェクトにとって現実的な選択肢となる。

## 実務での活用例

- デザインシステムのコンポーネント（カード、モーダル、タブなど）ごとに`@scope`でスタイルを区切り、他コンポーネントへの意図しない波及を防ぐ。
- サードパーティのウィジェットやCMSが生成するマークアップを埋め込む際、下限セレクタで自社スタイルの適用範囲外を明示し、埋め込みコンテンツ内部への影響を防ぐ（donut scope）。
- レガシーなグローバルCSSを段階的にリファクタリングする際、新規コンポーネントから`@scope`を導入し、既存の詳細度地獄を広げずに移行する。
- `@supports (color: scoped-value) ...`のようなfeature queryではなく`@supports at-rule(@scope)`等でサポート状況を判定し、非対応環境向けにフォールバックのスタイル戦略を用意する。

## 注意点

- Baselineの「Newly Available」は主要ブラウザの最新版で動作する状態を指すため、古いバージョンのブラウザや企業向けの長期サポートブラウザを対象とする場合は、フォールバックや段階的導入を検討する必要がある。
- `@scope`はカスケードの詳細度計算に影響を与えるため、既存の複雑なCSS設計に後から導入する際は、意図しない優先順位の変化がないか十分な検証が必要である。
- CSS-in-JSやCSS Modulesなど既存のスコープ手法と混在させる場合、責務の境界（どちらでスコープを管理するか）をチーム内で明確にしておかないと、かえって可読性が下がる恐れがある。

## 参考リンク

- [@scope CSS at-rule - CSS | MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/At-rules/@scope)
- [CSS @scope is now Baseline — Supported in All Major Browsers! - DEV Community](https://dev.to/zainmuhammad/css-scope-is-now-baseline-supported-in-all-major-browsers-4oeo)
- [Scoped Styles: the @scope rule | Can I use...](https://caniuse.com/css-cascade-scope)
- [How to @scope CSS Now That It's Baseline - Frontend Masters](https://frontendmasters.com/blog/how-to-scope-css-now-that-its-baseline/)
