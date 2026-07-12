# Declarative Partial Updates: HTMLをブラウザネイティブでストリーミング更新する新API

## 概要

Google I/O 2026でChromeチームが発表した「Declarative Partial Updates」は、HTMLの部分更新（サーバーからの差分反映やストリーミング挿入）をブラウザのネイティブ機能として提供する新しい仕様です。Chrome 148からフラグの背後で試験可能になっており、`<?marker>` や `<?start>` / `<?end>` といった処理命令（Processing Instruction）と `<template for>` 要素を使った順不同のHTMLストリーミング、そして `setHTML` / `streamHTML` / `appendHTML` / `streamAppendHTML` といった命名が統一された新しいJavaScript挿入APIの2本柱で構成されています。

## 背景

これまで「アイランドアーキテクチャ」や「out-of-order streaming」といった部分更新のパターンは、React Server ComponentsやHTMX、Remix、Quikなど各フレームワークがそれぞれ独自の仕組みで実装してきました。サーバー側で生成したHTMLの断片を、JavaScriptを介さずに正しい位置へ挿入したり、非同期に届いた内容を後から差し込んだりする処理は、フレームワークごとに実装が異なり、パフォーマンスやセキュリティ（XSS対策のサニタイズなど）の面でも差がありました。Declarative Partial Updatesは、この「部分更新」という共通の課題をプラットフォームレベルで解決しようとする試みです。

## なぜ重要なのか

第一に、フレームワークに依存しない標準APIとしてストリーミングHTML挿入が提供されることで、JavaScriptの実行や仮想DOMの差分計算に頼らずに動的なUI更新が可能になります。第二に、`setHTML`系のAPIにはサニタイザーがビルトインされており、`innerHTML`を直接操作するよりも安全にHTMLを挿入できます。第三に、Streams APIとの統合により、サーバーから届いた断片をそのまま非同期でページに反映でき、Time to Interactiveの改善やJavaScriptバンドルサイズの削減につながる可能性があります。フレームワーク側の実装コストが下がることで、今後Next.jsやRemixなどのメタフレームワークがこの標準APIを内部で採用していくことも見込まれます。

## 実務での活用例

- サーバーサイドレンダリングされたページの一部（コメント欄やレコメンドウィジェットなど）を、JavaScriptの追加ロードなしに`<template for>`と処理命令だけで後から差し込む
- 検索結果や無限スクロールのリストに対して`streamAppendHTML`を使い、Fetch/Streams APIで受け取ったチャンクをDOM操作なしに逐次追加する
- ユーザー入力を含むコンテンツを挿入する際に、標準サニタイザー付きの`setHTML`を使うことで、XSS対策のための追加ライブラリを減らす
- 既存のHTMXやTurboのような部分更新ライブラリのポリフィル/フォールバック戦略を検討する際の比較対象とする

## 注意点

- 2026年7月時点ではChrome 148でフラグを有効にした場合のみ動作する実験的機能であり、本番環境での利用は時期尚早です。
- Safari・Firefoxでの対応状況は未定であり、クロスブラウザ対応が必要なプロダクトでは当面ポリフィルやフレームワーク側の実装に頼る必要があります。
- 処理命令（Processing Instruction）を用いた構文はHTMLとしてはやや特殊な書き方になるため、既存のテンプレートエンジンやリンター、Lintルールとの相性を事前に確認する必要があります。
- 標準化はまだ議論段階であり、API名や構文が今後変更される可能性があります。

## 参考リンク

- [15 updates from Google I/O 2026: Powering the agentic web with new capabilities, tools, and features in Chrome](https://developer.chrome.com/blog/chrome-at-io26)
- [Declarative partial updates | Blog | Chrome for Developers](https://developer.chrome.com/blog/declarative-partial-updates)
