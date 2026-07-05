# Model Context Protocol(MCP)2026年仕様改定

## 概要

Model Context Protocol(MCP)は、AIアプリケーションが外部のツールやデータソースと接続するための標準プロトコルです。2026年7月28日に公開が予定されている新しい仕様(2026-07-28リリース候補)では、プロトコルのステートレス化、Tasks拡張、MCP Apps、認可の強化など、実運用を見据えた大きな改定が行われています。

## 背景

MCPは2024年11月の登場以降、急速に普及し、GitHub・Slack・Notion・Stripe・Salesforce・Xなど多数の企業が公式サーバーを提供するようになりました。Glamaのレジストリでは、登場当初の約100個から2026年3月時点で19,000個を超えるサーバーが登録されるまでにエコシステムが拡大しています。利用の広がりに伴い、単一セッションに依存したアーキテクチャや長時間実行タスクの扱い、認可の仕組みなど、本番運用における課題が明らかになってきました。2026年のMCPロードマップでは、トランスポートのスケーラビリティ・エージェント間通信・ガバナンスの成熟・エンタープライズ対応の4テーマを軸に改善が進められています。

## なぜ重要なのか

今回の改定の中核は、プロトコル層のステートレス化です。従来はセッションの状態をサーバー側で保持する必要があり、スケールアウト時にスティッキーセッションの管理が必須でした。ステートレス化により、単純なラウンドロビン型のロードバランサーでMCPサーバーをスケールできるようになり、運用の複雑さが大幅に下がります。また、長時間かかる処理を`tasks/get`・`tasks/update`・`tasks/cancel`で管理できるTasks拡張や、サーバーがサンドボックス化されたiframe内でインタラクティブなUIを提供できるMCP Appsなど、実務での使い勝手を高める機能が追加されています。さらに、OAuth 2.1が仕様として必須化されることで、認可周りのセキュリティ基盤も強化されます。

## 実務での活用例

- 複数インスタンスで動作するMCPサーバーを、セッション同期の仕組みを自前で構築せずにロードバランサー配下でスケールさせる
- 画像生成やバッチ処理など完了に時間がかかるツール呼び出しを、Tasks拡張を使って非同期的にポーリング・キャンセルできるようにする
- MCP Appsを利用し、ツール実行結果をチャット内にインタラクティブなUIとして表示する(ホスト側で事前にプリフェッチ・セキュリティレビューが可能)
- OAuth 2.1準拠を前提に、社内向けMCPサーバーの認可フローを設計・実装する

## 注意点

仕様改定によりステートフルな実装からの移行が必要になる場合があり、既存のMCPサーバー・クライアント実装が新仕様にどこまで追随しているかを事前に確認する必要があります。また、MCP Appsのようにサーバーが提供するUIをホスト側で実行する仕組みは、悪意あるツールUIやプロンプトインジェクションのリスクも指摘されており、セキュリティレビューの体制整備が求められます。仕様はまだリリース候補(RC)の段階であり、最終仕様公開までに変更される可能性がある点にも留意が必要です。

## 参考リンク

- [The 2026-07-28 MCP Specification Release Candidate](https://blog.modelcontextprotocol.io/posts/2026-07-28-release-candidate/)
- [The 2026 MCP Roadmap](https://blog.modelcontextprotocol.io/posts/2026-mcp-roadmap/)
- [MCP's biggest growing pains for production use will soon be solved - The New Stack](https://thenewstack.io/model-context-protocol-roadmap-2026/)
- [Model Context Protocol overhaul introduces new security challenges for developers | SC Media](https://www.scworld.com/brief/model-context-protocol-overhaul-introduces-new-security-challenges-for-developers)
- [MCP Adoption Statistics 2026: Model Context Protocol](https://www.digitalapplied.com/blog/mcp-adoption-statistics-2026-model-context-protocol)
