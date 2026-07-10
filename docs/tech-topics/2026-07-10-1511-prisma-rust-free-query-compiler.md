# Prisma 7：Rustエンジンを捨てTypeScript/WASMへ移行したクエリコンパイラ

## 概要

Prisma ORMの最新メジャーバージョンであるPrisma 7では、これまでクエリ実行を担ってきたRust製のネイティブバイナリ（クエリエンジン）が廃止され、TypeScriptとWebAssembly（WASM）で実装された新しい「Query Compiler」に置き換えられた。これにより、Prismaは主要な依存関係からネイティブバイナリを排除し、純粋にJavaScript/WASMだけで動作するORMとなった。公式ベンチマークでは、25,000件のレコードを取得する`findMany`が185msから55msへ、複雑なJOINを伴うクエリも207msから130msへと大幅に高速化したと報告されている。加えて、エンジン本体のサイズも従来比85〜90%削減され、圧縮後で約600KBまで小さくなった。

## 背景

Prismaは元々、パフォーマンスと型安全性を両立させるためにRustでクエリエンジンを実装し、Node.jsからネイティブバイナリを呼び出す構成を採用していた。しかしこの構成は、プラットフォームごとに異なるバイナリを配布する必要があり、Dockerイメージの肥大化や、Cloudflare WorkersやDeno、Bunといったネイティブバイナリの実行をサポートしない、あるいは制限のあるエッジ・サーバーレス環境での動作に制約を生んでいた。Prismaチームはこの課題を解決するため、クエリのコンパイルとSQL生成のロジックをTypeScriptで書き直し、実行時のパフォーマンスが必要な部分のみをWASMモジュールとして提供する「Query Compiler」プロジェクトを進めてきた。2025年から段階的にアーリーアクセス版が提供され、2026年にPrisma 7として正式リリースされた。

## なぜ重要なのか

ネイティブバイナリへの依存がなくなったことで、PrismaはCloudflare Workers、Vercel Edge Functions、Deno、Bunといった多様な実行環境で追加の設定なしに動作できるようになった。これはエッジコンピューティングやサーバーレス環境を前提とするフルスタックTypeScript開発において大きな意味を持つ。また、バイナリのダウンロードやプラットフォーム判定が不要になることで、CI/CDのビルド時間短縮やDockerイメージサイズの削減にもつながる。パフォーマンス面でも、単なる「軽量化」ではなく実クエリで数倍の高速化が確認されており、アーキテクチャの単純化と性能向上を同時に達成した事例として注目されている。

## 実務での活用例

- Cloudflare WorkersやVercel Edge Functionsなど、ネイティブバイナリの実行に制約があった環境で、Prismaを使ったデータアクセス層をエッジ側に移設する。
- 既存プロジェクトをPrisma 7へアップグレードする際に、`findMany`や複雑なJOINを含むクエリのレスポンスタイムを移行前後で計測し、実際の改善幅を確認する。
- Dockerベースのデプロイパイプラインで、Prismaのネイティブバイナリ配布に起因していたイメージサイズやビルド時間の問題を解消する。
- Bun・Deno環境でのAPIサーバー構築時に、Prismaのセットアップがネイティブバイナリの互換性問題なく行えるかを検証する。

## 注意点

- Rustエンジンから移行する際は、内部的な挙動の差異（一部のクエリの実行順序やエラーメッセージなど）が生じる可能性があるため、本番導入前にステージング環境での回帰テストが推奨される。
- サードパーティのツールやプラグインがPrisma 7の新しいQuery Compilerに対応済みかどうかを事前に確認する必要がある。
- 「Rustフリー」と謳われてはいるものの、WASMモジュール自体はRustベースのツールチェインでビルドされている部分があり、完全にRustが排除されたわけではない点には留意する。

## 参考リンク

- [Prisma ORM 7: They Ditched Rust and Everything Got Faster (Prisma Blog)](https://www.prisma.io/blog/announcing-prisma-orm-7-0-0)
- [Rust to TypeScript Update: Boosting Prisma ORM Performance (Prisma Blog)](https://www.prisma.io/blog/rust-to-typescript-update-boosting-prisma-orm-performance)
- [Prisma ORM Architecture Shift: Why We Moved from Rust to TypeScript (Prisma Blog)](https://www.prisma.io/blog/from-rust-to-typescript-a-new-chapter-for-prisma-orm)
