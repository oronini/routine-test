# GitHub Agentic Workflows、パブリックプレビューへ ― Markdownで書くリポジトリ自動化

## 概要

GitHubは2026年6月、Actions上でAIコーディングエージェントを走らせる新機能「GitHub Agentic Workflows」をパブリックプレビューとして公開した。開発者は自然言語のMarkdownファイルに自動化したいタスクを記述するだけで、それがGitHub Actionsの標準的なYAMLワークフローへコンパイルされる。イシューのトリアージ、CI失敗時の原因分析、ドキュメント更新といった「推論を伴うタスク」を、GitHub Copilot・Anthropic Claude・Google Gemini・OpenAI Codexなど複数のAIエンジンから選んで自動化できる点が特徴である。2026年2月のテクニカルプレビューから、パーソナルアクセストークン必須という制約が撤廃され、標準の`GITHUB_TOKEN`だけで動作するようになった。

## 背景

これまでGitHub Actions上でAIエージェントを使った自動化を組もうとすると、YAMLの記述に加えて、エージェント呼び出し・権限管理・出力の検証といった作業を開発者自身が個別に実装する必要があった。特に「読み取りはさせたいが書き込みは慎重に制御したい」といった権限設計や、エージェントの出力をそのままリポジトリに反映することへのセキュリティ上の懸念が、実運用への導入を難しくしていた。GitHub Agentic Workflowsは、こうした定型的な足回り（権限のデフォルト最小化、書き込み操作の検証、シークレットの隔離など）をプラットフォーム側で標準化することで、自然言語による自動化定義を安全に実現しようとする試みである。

## なぜ重要なのか

最大の特徴は、自動化のロジックをYAMLではなくMarkdown（フロントマター＋自然言語の指示文）で記述できる点にある。書いたMarkdownファイルは`.lock.yml`という「ハードニング済み」のActionsワークフローファイルにコンパイルされ、デフォルトブランチにコミットして使う。セキュリティ面では、ワークフローはデフォルトで読み取り専用権限のみを持ち、イシュー作成やコメント追加、プルリクエスト作成といった書き込み操作はフロントマターで宣言した「safe-outputs」を通じてのみ許可される。シークレットもエージェントの実行環境には直接渡さず、隔離された後続ジョブに閉じ込める設計になっている。既存のランナーグループやポリシー制約をそのまま再利用できるため、組織のガバナンスを崩さずにAI自動化を段階的に導入できる点が、実務上の採用障壁を下げている。

## 実務での活用例

たとえば「新規イシューが作成されたら、内容を読んでラベルを提案し、関連する既存イシューがあればリンクする」といったトリアージ作業や、「CIが失敗したら失敗ログを解析し、原因の要約と修正候補をプルリクエストのコメントとして投稿する」といった一次対応を、Markdownの指示文とイベントトリガー（issue作成、スケジュール実行など）を組み合わせるだけで自動化できる。ドキュメントの棚卸しや、古いコード例の更新提案なども、スケジュール実行のワークフローとして組みやすい。エージェントの出力は安全な出力チャネル経由でしかリポジトリに反映されないため、「AIによる自動コメント・自動PR」を段階的に本番運用へ組み込みやすいのが利点である。

## 注意点

2026年7月時点ではまだパブリックプレビュー段階の機能であり、仕様や挙動が今後変更される可能性がある。AIエンジンの呼び出しにはAIクレジットの消費が伴うため、組織で導入する際はコスト管理（誰にどれだけ実行を許可するか）を事前に設計しておく必要がある。また、read-onlyデフォルトやsafe-outputsによる保護があるとはいえ、外部からの入力（イシュー本文やPRコメントなど）を指示として解釈するエージェントである以上、プロンプトインジェクションのリスクはゼロではない。エージェントに渡す権限とワークフローがトリガーされる条件（誰でもイシューを作成できるかなど）を絞り込み、生成された`.lock.yml`の内容もレビューした上で導入することが望ましい。

## 参考リンク

- [GitHub Agentic Workflows is now in public preview - GitHub Changelog](https://github.blog/changelog/2026-06-11-github-agentic-workflows-is-now-in-public-preview/)
- [Automate repository tasks with GitHub Agentic Workflows - The GitHub Blog](https://github.blog/ai-and-ml/automate-repository-tasks-with-github-agentic-workflows/)
- [About GitHub Agentic Workflows - GitHub Docs](https://docs.github.com/en/copilot/concepts/agents/about-github-agentic-workflows)
- [Home | GitHub Agentic Workflows](https://github.github.com/gh-aw/)
- [GitHub Agentic Workflows are now in technical preview - GitHub Changelog](https://github.blog/changelog/2026-02-13-github-agentic-workflows-are-now-in-technical-preview/)
