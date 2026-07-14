# Laravel 13.16の新コマンド「php artisan dev」― 開発プロセスを1コマンドに統合

## 概要

2026年6月17日にリリースされたLaravel 13.16.0で、新たに `php artisan dev` コマンドが追加された。このコマンドは、開発サーバー、キューワーカー、ログのtail出力、Viteの4つのプロセスを1つのコマンドで同時に起動し、それぞれの出力を色分けして表示する。従来 `composer.json` の `scripts` に定義されていた `composer run dev` を置き換えるもので、設定をコンポーザーの設定ファイルではなく、アプリケーションコード側（`DevCommands` クラス）に移す点が特徴である。

## 背景

Laravelでは以前から、開発時にサーバー・キューワーカー・Vite・ログ監視など複数のプロセスを並行して立ち上げる必要があり、`concurrently` などのNode系ツールを利用した `composer run dev` スクリプトが標準の雛形として提供されてきた。しかしこの方式は設定が `composer.json` に埋め込まれており、Artisanコマンド体系との一貫性がない、追加のプロセスを差し込みにくいといった課題があった。`php artisan dev` は、この設定をPHPのサービスプロバイダ経由で記述できる `DevCommands` という仕組みに置き換えることで、Laravel本来の「Artisanコマンドとして管理する」流儀に統一した。

## なぜ重要なのか

開発体験（DX）の改善は近年のフレームワーク間競争における主要な差別化要因の一つであり、Laravelはこの `php artisan dev` によって「クローン後すぐに1コマンドでフルスタックの開発環境が立ち上がる」体験をより洗練させた。特に、npm・yarn・pnpm・bunといったNodeパッケージマネージャを自動検出する点や、Reverb（WebSocketサーバー）やStripe CLIのWebhookリスナーなど、サードパーティのツールをサービスプロバイダから追加登録できる拡張性を持つ点は、実運用時のチーム開発において設定の属人化を防ぎ、オンボーディングコストを下げる効果が期待できる。

## 実務での活用例

- 新規プロジェクトのREADMEやセットアップ手順から `composer run dev` を `php artisan dev` に置き換え、Artisanコマンド体系に統一する。
- Reverb（リアルタイム通信）やStripe CLIのWebhookリスナーなど、プロジェクト固有の常駐プロセスをサービスプロバイダ経由で `DevCommands` に追加登録し、チーム全員が同じ開発環境を1コマンドで再現できるようにする。
- CI環境や本番環境ではなく、あくまでローカル開発専用のコマンドとして位置づけ、`.env` の設定と組み合わせてオンボーディング手順を簡素化する。

## 注意点

- `php artisan dev` はLaravel 13.16.0以降で利用可能な機能であり、それ以前のバージョンでは従来通り `composer run dev` を使う必要がある。
- 複数プロセスを同時起動する仕組みであるため、ポート競合や既存の `Procfile` ベースの運用（Herd、Sailなど）との併用時には設定の重複がないか確認が必要である。
- 本番環境向けのプロセス管理（supervisorやsystemdなど）を代替するものではなく、あくまでローカル開発体験を高めるためのコマンドである点に留意する。

## 参考リンク

- [The artisan dev Command in Laravel 13.16.0 - Laravel News](https://laravel-news.com/laravel-13-16-0)
- [Laravel 13.16 Ships the New artisan dev Command - Laravel Magazine](https://laravelmagazine.com/laravel-13-16-ships-the-new-artisan-dev-command)
