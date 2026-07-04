# WordPress 7.0「Armstrong」、AI連携を標準化するConnectors APIを搭載

## 概要

2026年5月20日、WordPressはメジャーバージョン7.0（開発コードネーム「Armstrong」）を正式リリースした。今回の目玉機能は、外部のAIプロバイダーとの連携を標準化する「Connectors API」と「WP AI Client SDK」からなるネイティブAIフレームワークである。管理画面の「設定 → コネクター」画面からOpenAI（ChatGPT）・Google（Gemini）・Anthropic（Claude）といった主要プロバイダーを選択・認証情報を設定するだけで、対応するプラグインやテーマがそのAI接続を横断的に利用できるようになった。あわせて、レスポンシブ表示制御が可能なブロックの表示・非表示設定や、パンくずリスト用の「Breadcrumbs」ブロック、アイコン挿入用の「Icons」ブロックなど、ブロックエディタ自体の機能拡張も行われている。

## 背景

これまでWordPress上でAI機能を提供するプラグインは、それぞれが個別にAPIキーの管理や外部プロバイダーとの通信処理を実装しており、プラグインごとに認証情報を別々に入力する必要があったり、実装品質やセキュリティ対策にばらつきが生じたりする課題があった。WordPressコミュニティはこの状況を解消するため、AI連携部分を一つの共通レイヤーとしてコア機能に組み込む方針を進め、Make WordPress Coreのフィールドガイドを通じて開発が公開されてきた。7.0ではこの取り組みが実を結び、Connectors APIによってAIプロバイダーへの接続設定をサイト全体で一元管理できるようになり、プラグイン・テーマ開発者はWP AI Client SDK経由でその接続を再利用できる設計になった。

## なぜ重要なのか

WordPressは世界のWebサイトの相当数を占めるCMSであり、そのコア機能としてAI連携の共通基盤が標準搭載された意味は大きい。個々のプラグインが独自にAI呼び出しを実装する必要がなくなることで、認証情報の重複管理によるセキュリティリスクや、プロバイダーごとの実装差異に起因する不具合を減らせる。また、サイト運営者からすればAIプロバイダーの選択・切り替えを一箇所で行えるようになり、特定ベンダーへのロックインを避けやすくなる。CMS層でのAI標準化は、React Compilerによるフロントエンド最適化の自動化や、Next.jsのフレームワーク標準機能拡充などと同様に、「個々の開発者の実装依存だった機能をプラットフォーム側の標準機能に引き上げる」という2026年の潮流を象徴する動きといえる。

## 実務での活用例

- 既存サイトで複数のAI関連プラグインを併用している場合、各プラグインが独自に保持していたAPIキーをConnectors APIの共通設定に統合し、認証情報の管理箇所を一本化する。
- プラグイン・テーマ開発者は、WP AI Client SDKを利用して自作機能にAI呼び出しを組み込み、利用者側のプロバイダー選択（OpenAI・Google・Anthropicなど）に依存しない実装にする。
- コンテンツ運用チーム向けに、新しいBreadcrumbsブロックやIconsブロックを使って、外部プラグインなしでナビゲーション表示やアイコン装飾を行う運用ルールを整備する。
- レスポンシブ表示制御機能を使い、モバイル・デスクトップで表示するブロックを出し分けることで、デバイスごとのUI最適化をプラグインに頼らず行う。

## 注意点

- Connectors APIやAI Abilities APIはコアに統合された新しい機能であるため、既存のAI連携プラグインとの互換性や移行手順を事前に検証する必要がある。
- 認証情報を一元管理する設計になった分、コネクター設定へのアクセス権限を持つ管理ユーザーの範囲を適切に制限し、意図しないAPIキーの流出・悪用を防ぐ権限設計が重要になる。
- 外部AIプロバイダーへの接続を伴うため、サイトが扱うコンテンツやユーザーデータをどこまでAIプロバイダーに送信してよいか、プライバシーポリシーや利用規約の観点から確認しておく必要がある。

## 参考リンク

- [WordPress 7.0 Launched: Admin Interface, AI Features & New Blocks](https://hostwp.io/blog/wordpress-7-0-admin-refresh-ai-connectors-new-blocks/)
- [What's New in WordPress 7.0? (Features & Screenshots) - WPBeginner](https://www.wpbeginner.com/news/whats-new-in-wordpress-7-0/)
- [WordPress 7.0 Field Guide – Make WordPress Core](https://make.wordpress.org/core/2026/05/14/wordpress-7-0-field-guide/)
- [WordPress 7.0 Armstrong release: AI Abilities API, Command Palette, migration - WPPoland](https://wppoland.com/en/wordpress-7-0-complete-guide-ai-integration/)
- [What's New in WordPress 7.0? A Community Member's Perspective - WP Engine](https://wpengine.com/blog/wordpress-7-0-release/)
