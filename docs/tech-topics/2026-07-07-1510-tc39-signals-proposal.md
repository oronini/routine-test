# TC39 Signals提案とJavaScriptリアクティビティの標準化

## 概要

TC39（ECMAScriptの標準化を担う委員会）では、フロントエンドフレームワーク間で異なる実装がなされてきた「シグナル（Signals）」というリアクティビティの仕組みを、JavaScript言語そのものに組み込もうとする提案が進行中です。シグナルとは、状態（state）とそこから派生する計算（computation）を有向非巡回グラフとしてモデル化し、値の変化を自動的かつ効率的に伝播させるデータ型です。Angular、Vue、Solid、Preact、Qwik、MobXなど、主要なフレームワークの設計者が議論に参加しており、フレームワークを横断したリアクティビティの共通基盤を目指す動きとして注目されています。

## 背景

これまでReact、Vue、Solid、Angularなどのフレームワークは、それぞれ独自の方式で状態変化の検知と再描画を実現してきました。Reactは仮想DOMによる差分検出を採用する一方、Vue 4やSolidJSはSignalsを中核のリアクティビティモデルとして全面的に採用しています。Angularも22でSignal Formsを安定版として導入するなど、Signalsへの移行が加速しています。こうした背景の中、フレームワークごとに似て非なる実装が乱立する状況を解消し、言語レベルで共通の仕組みを提供しようという機運が高まり、TC39にSignals提案が持ち込まれました。ただし、提案自体は現時点でStage 1にとどまっており、Stage 2への昇格には至っていません。

## なぜ重要なのか

もしSignalsが言語標準として採用されれば、フレームワーク間での相互運用性が大きく向上し、ライブラリ作者は特定のフレームワークに依存しないリアクティブなロジックを記述できるようになります。また、ブラウザやランタイムレベルでの最適化が可能になることで、パフォーマンス面でも恩恵が期待されます。一方で、Reactは仮想DOMベースの独自路線を維持しており、Signalsへの追随には消極的とされています。そのため、標準化が実現しても、エコシステム全体で統一的に採用されるかどうかは不透明であり、今後の議論の行方を注視する価値があります。

## 実務での活用例

- Vue 4やSolidJSの `signal`/`ref` APIを使い、コンポーネント間で共有する状態を細粒度に更新し、不要な再レンダリングを削減する。
- Angular 22のSignal Formsを用いて、フォームの入力値と検証状態をリアクティブに管理し、変更検知のオーバーヘッドを抑える。
- ライブラリ作者がフレームワーク非依存のリアクティブなステート管理ロジックを実装する際、将来的な言語標準Signalsへの移行を見据えた設計を検討する。

## 注意点

- TC39 Signals提案はStage 1の段階であり、仕様が確定するまでには数年単位の時間がかかる可能性がある。
- Reactは現時点でSignalsモデルを採用しておらず、エコシステム全体での統一的な採用は保証されていない。
- 各フレームワークの現行Signals実装（Vue、Solid、Angularなど）はAPIやセマンティクスが微妙に異なるため、将来の標準化に伴って移行コストが発生する可能性がある。

## 参考リンク

- [tc39/proposal-signals: A proposal to add signals to JavaScript.](https://github.com/tc39/proposal-signals)
- [tc39/proposals: Tracking ECMAScript Proposals](https://github.com/tc39/proposals)
- [[2026 Edition] The Stat Management War is Over | Reactivity Without Frameworks Brought by TC39 "Standard Signals"](https://tasukehub.com/articles/standard-signals-2026)
- [10 JavaScript Trends in 2026 (Frameworks, Toolchains)](https://www.alphonsolabs.com/javascript-trends-2026/)
