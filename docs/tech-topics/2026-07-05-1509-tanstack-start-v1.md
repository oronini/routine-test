# TanStack Start、v1リリース候補（RC）へ到達しNext.jsの代替として存在感

## 概要

TanStack Routerをベースにしたフルスタックフレームワーク「TanStack Start」が、v1のリリース候補（Release Candidate）段階に到達した。機能的にはほぼ完成し、APIも安定版とみなせる段階に入っている。フルドキュメントSSR、ストリーミング、サーバー関数、クライアント/サーバービルドなど、モダンなフルスタックフレームワークに求められる機能一式を備えており、ReactだけでなくSolidJSにも対応する点が特徴である。2026年の Open Source Awards（アムステルダム）では「Breakthrough of the Year」を受賞するなど、コミュニティからの評価も高まっている。

## 背景

TanStack Startは、TanStack Query・TanStack Table・TanStack Routerなどで知られるTanStackエコシステムの一員として開発が進められてきたフルスタックフレームワークである。TanStack Routerが持つ型推論の仕組みを土台に、SSRやサーバーサイドの処理を追加する形で設計されており、ルーティング機能とフルスタック機能を完全に統合している点が他フレームワークとの大きな違いとなっている。近年はVercel製のNext.jsが事実上の標準として使われてきたが、特定ベンダーへの依存を避けたい、あるいはVite中心のエコシステムを使い続けたいという開発者から、TanStack Startが有力な選択肢として注目されるようになった。

## なぜ重要なのか

TanStack Startが安定版に近づいたことで、Reactエコシステムにおける「Next.js以外のフルスタックフレームワーク」の選択肢が現実的な運用レベルに達した点が大きい。ルーティングからサーバー関数、APIルート、ミドルウェアまでを型安全に一貫させる設計は、フロントエンドとバックエンドの境界で型がずれるという従来ありがちな問題を解消する。またVercelのようなホスティング事業者に縛られず、Cloudflare Workers・Netlify・任意のNode/Bunランタイムなど、デプロイ先を柔軟に選べる「ユニバーサルデプロイ」を志向している点も、特定ベンダーへのロックインを避けたい企業にとって重要な意味を持つ。

## 実務での活用例

- 新規プロジェクトで、Next.jsに依存しないフルスタックReact（またはSolidJS）アプリケーションを構築する際の土台として採用する。
- `createServerFn`によるサーバー関数を使い、ローダーやコンポーネントからバリデーション付きでサーバー専用処理を呼び出し、クライアント/サーバー間のRPCを型安全に実装する。
- ストリーミングSSRを活用し、準備できた部分から順にHTMLをクライアントへ送出することで、体感的な初期表示速度を改善する。
- Cloudflare WorkersやNode/Bunランタイムなど、組織の既存インフラに合わせてデプロイ先を柔軟に選択する。

## 注意点

- v1は現時点でリリース候補段階であり、正式な1.0リリースではないため、本番導入前にはドキュメントの更新状況や破壊的変更の有無を確認する必要がある。
- Next.jsと比較すると採用実績やエコシステム（プラグイン、学習リソース、事例）はまだ少なく、大規模プロジェクトへの導入は情報収集を十分に行った上で判断すべきである。
- ルーティングをTanStack Routerに全面的に依存する設計のため、既存プロジェクトからの移行では、ルーティングの設計思想の違いを踏まえた学習コストが発生する点に留意する。

## 参考リンク

- [TanStack Start v1 Release Candidate | TanStack Blog](https://tanstack.com/blog/announcing-tanstack-start-v1)
- [TanStack Start Overview | TanStack Start React Docs](https://tanstack.com/start/latest/docs/framework/react/overview)
- [TanStack Start: A New Meta Framework Powered by React or SolidJS - InfoQ](https://www.infoq.com/news/2025/11/tanstack-start-v1/)
