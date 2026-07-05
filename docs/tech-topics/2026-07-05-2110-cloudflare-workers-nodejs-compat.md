# Cloudflare Workers が Node.js API 互換性を大幅拡大

## 概要

Cloudflareは、エッジ実行環境「Cloudflare Workers」において、Node.jsのAPI互換性を継続的に拡大している。`nodejs_compat`互換フラグを有効にするだけで、`node:child_process`、`node:perf_hooks`、`node:readline`、`node:dgram`といった主要モジュールが利用可能になっており、既存のNode.js向けエコシステム（npmパッケージ群）をエッジ環境でもそのまま動かせる方向に進化を続けている。多くの実装はポリフィルではなく、TypeScriptとC++によるネイティブ実装で提供されている点が特徴である。

## 背景

Cloudflare Workersは、V8 Isolatesベースの軽量なサーバーレス実行環境として、コールドスタートの速さとグローバル分散を強みにしてきた。一方で、Node.js固有のAPI（`fs`やネイティブモジュールなど一部を除く）が使えないことが、既存のNode.jsアプリケーションやライブラリをそのまま移植する際の障壁となっていた。

2025年から2026年にかけて、Cloudflareはこの障壁を取り除くべく「1年間かけたNode.js互換性の改善」を継続的に発表しており、対応するAPIの数を大きく増やしてきた。背景には、Deno・Bunといった他のJavaScriptランタイムもNode.js互換性を競うように強化しており、サーバーサイドJavaScriptのランタイム選定において「Node.js API」が事実上の共通言語になりつつあるという業界全体の潮流がある。

## なぜ重要なのか

- **移植コストの低減**: npmエコシステムの既存資産（ライブラリ、フレームワーク）をほぼそのままエッジ環境に持ち込めるようになり、Workers向けに書き直すコストが減る。
- **ランタイム間の相互運用性向上**: Node.js、Deno、Bun、Cloudflare Workersが同じWeb標準API・Node.js APIに収束することで、開発者はランタイムを問わず同じコードベースを再利用しやすくなる。
- **ネイティブ実装によるパフォーマンス**: ポリフィルに頼らずネイティブ実装を進めていることで、エッジでの実行速度やメモリ効率が犠牲になりにくい。
- **エコシステムの成熟**: これまでエッジ環境では動かなかった認証・ロギング・ストリーム処理などのミドルウェアが動作するようになり、フルスタックフレームワークのデプロイ先としての選択肢が広がる。

## 実務での活用例

- 既存のExpress系ミドルウェアや`stream`・`path`・`buffer`などのNode.js標準モジュールに依存するライブラリを、書き換えなしでCloudflare Workers上のAPIエンドポイントとして稼働させる。
- Next.jsやRemixなどのフルスタックフレームワークをCloudflareのエッジにデプロイする際、Node.js依存部分の非互換によるビルドエラーが減り、デプロイ検証の工数を削減する。
- ローカル開発ではBunやNode.jsでテストし、本番はCloudflare WorkersやDeno Deployにデプロイするといった、ランタイムをまたいだ開発フローが取りやすくなる。

## 注意点

- 対応APIは`wrangler.toml`（または`wrangler.jsonc`）の互換性日付（compatibility date）に依存するため、古い互換性日付のままでは新しいNode.js APIが有効にならない場合がある。
- すべてのNode.js APIが完全網羅されているわけではなく、`fs`のようにサンドボックス環境の制約上サポートが難しいAPIも存在するため、移植前に対応状況を公式ドキュメントで確認する必要がある。
- ネイティブ実装への移行が進行中であるため、バージョンやリリース時期によって挙動が変わる可能性があり、本番運用前には実際のワークロードでの検証が推奨される。

## 参考リンク

- [Node.js compatibility · Cloudflare Workers docs](https://developers.cloudflare.com/workers/runtime-apis/nodejs/)
- [A year of improving Node.js compatibility in Cloudflare Workers](https://blog.cloudflare.com/nodejs-workers-2025/)
- [More NPM packages on Cloudflare Workers: Combining polyfills and native code to support Node.js APIs](https://blog.cloudflare.com/more-npm-packages-on-cloudflare-workers-combining-polyfills-and-native-code/)
- [More Node.js APIs in Cloudflare Workers — Streams, Path, StringDecoder](https://blog.cloudflare.com/workers-node-js-apis-stream-path/)
- [Cloudflare WorkersがNode.js API互換の提供を発表。Bun、Denoなどに続く対応により、Node.js APIはサーバサイドJavaScriptの事実上の標準になるか](https://www.publickey1.jp/blog/23/cloudflare_workersnodejs_apibundenonodejs_apijavascript.html)
