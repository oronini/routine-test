# Bun v1.3: フルスタック機能を統合したJavaScriptランタイムの進化

## 概要

高速なJavaScript/TypeScriptランタイムとして知られる Bun が、v1.3 で大型アップデートを行いました。目玉は複数のデータベースに対応する統一API「Bun.SQL」と、設定不要（ゼロコンフィグ）でホットモジュールリプレースメント（HMR）やReact Fast Refreshが動くフロントエンド開発サーバーです。加えて、TC39標準のデコレータ構文への対応、ネイティブREPL、`--compile --target=browser` によるブラウザ向け自己完結型HTMLビルドなど、単体のランタイムでフルスタック開発を完結させる方向性がさらに強まりました。

## 背景

BunはZigで書かれた高速な全部入りJavaScriptランタイムとして登場し、パッケージマネージャー、バンドラー、テストランナーを単一バイナリに統合することで、Node.jsエコシステムにおける「ツールの寄せ集め」問題を解消しようとしてきました。v1.0以降は安定性と互換性の向上に注力していましたが、v1.3ではその範囲をさらに広げ、これまで外部ライブラリ（`pg`、`mysql2`、`better-sqlite3`など）に頼っていたデータベースアクセス層までランタイム標準機能として取り込みました。これは「アプリケーションの土台をすべてBunだけで完結させる」という開発チームの一貫した方針の延長線上にあります。

## なぜ重要なのか

- **依存関係の削減**: Bun.SQLによりMySQL、MariaDB、PostgreSQL、SQLiteへのアクセスが外部パッケージなしで可能になり、サプライチェーンリスクやバージョン管理の負担が減ります。
- **開発体験の統一**: フロントエンドのHMRやReact Fast Refreshがゼロコンフィグで動くため、Viteなどのバンドラー設定を別途書かずにHTMLファイルを直接実行して開発を始められます。
- **配布の簡素化**: `--compile --target=browser` により、ビルド成果物をブラウザで動く自己完結型HTMLとして出力できるため、デプロイやデモ共有の手間が減ります。
- **言語仕様への追従**: TC39標準のECMAScriptデコレータをネイティブサポートしたことで、NestJS的なデコレータベースの設計パターンをトランスパイラなしで扱えるようになりました。

## 実務での活用例

- 小〜中規模のAPIサーバーで、PostgreSQLやSQLiteへの接続に`pg`や`prisma`のような追加パッケージを入れず、`Bun.SQL`だけでクエリを発行する。
- 社内ツールやプロトタイプで、Viteの設定ファイルを書かずにHTMLエントリーポイントをそのまま`bun --hot`で起動し、素早くUIを確認する。
- CIやデモ環境向けに、`bun build --compile --target=browser`でスタンドアロンHTMLを生成し、サーバーなしで動作確認できる成果物を配布する。
- デコレータ構文を使うバックエンドフレームワーク（NestJS的な設計）をBun上でトランスパイル設定なしに動かす。

## 注意点

- Bun.SQLは統一APIである一方、各データベース固有の高度な機能（レプリケーション制御や拡張機能など）は既存の専用ドライバの方が柔軟な場合があります。移行前に必要な機能がカバーされているか確認が必要です。
- Bunは活発に開発が続いており、過去にはメモリリーク関連の不具合報告もありました。本番投入時はパッチバージョンの追跡とロールバック手順の準備が推奨されます。
- ゼロコンフィグ機能は小〜中規模プロジェクト向けの利便性が高い一方、大規模なフロントエンド構成では既存のVite/Webpack設定との共存や移行コストを見極める必要があります。

## 参考リンク

- [Bun — A fast all-in-one JavaScript runtime](https://bun.com/)
- [GitHub - oven-sh/bun: Incredibly fast JavaScript runtime, bundler, test runner, and package manager – all in one](https://github.com/oven-sh/bun)
- [Bun Releases (GitHub)](https://github.com/oven-sh/bun/releases)
- [Bun v1.3.0 Release Notes (GitHub Release Tag)](https://github.com/oven-sh/bun/releases/tag/bun-v1.3.0)
- [Bun Introduces Built-in Database Clients and Zero-Config Frontend Development - InfoQ](https://www.infoq.com/news/2026/01/bun-v3-1-release/)
