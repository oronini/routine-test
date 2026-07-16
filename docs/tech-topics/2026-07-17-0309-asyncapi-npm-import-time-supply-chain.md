# AsyncAPI npmサプライチェーン侵害とimport時ペイロード配信

## 概要

2026年7月14日、Microsoft脅威インテリジェンスは、AsyncAPI npm組織に対する協調的なサプライチェーン侵害を報告した。`@asyncapi/specs`や`@asyncapi/generator`など4パッケージ・5バージョンが約90分の間に立て続けに公開され、いずれのエントリポイントにも同一の難読化ローダーが仕込まれていた。今回の攻撃で特に注目すべき点は、悪意あるコードが`npm install`時のpostinstallスクリプトではなく、パッケージが`require()`や`import`されたタイミング（import時）で実行される点である。この方式は、サプライチェーン対策として広く知られる`--ignore-scripts`オプションを無効化してしまう。

## 背景

npmエコシステムを狙ったサプライチェーン攻撃はここ数年で急増しており、多くはpostinstallなどのライフサイクルフックを悪用してインストール直後に不正コードを実行する手口だった。この手口に対する代表的な防御策が、ライフサイクルスクリプトの実行を止める`npm install --ignore-scripts`である。しかし今回のAsyncAPI侵害では、攻撃者は`pull_request_target`を用いる脆弱なGitHub Actionsワークフローを悪用してボットのPersonal Access Tokenを窃取し、各パッケージのエントリポイント自体に難読化ローダーを注入した。これにより、インストール時のフックを一切経由せず、パッケージがコード内で読み込まれた瞬間に不正コードが起動する構造になっていた。回収されたペイロードは「Miasma」と呼ばれるランタイムで、C2通信や永続化機構に加え、Nostr・Ethereum・BitTorrent DHTを用いた分散フォールバック通信、IPFS経由の第2段階ペイロード配信など、検知回避を意識した多層的な設計を持っていた。

## なぜ重要なのか

`--ignore-scripts`はこれまで「サプライチェーン攻撃対策のベストプラクティス」として広く推奨されてきたが、import時に実行されるマルウェアに対してはこの対策が機能しない。つまり、インストール時の防御だけに依存しているチームは、実際には侵害されたパッケージをコード内で使用した瞬間に無防備な状態に陥る。また、攻撃の起点がGitHub Actionsのパイプライン侵害（`pull_request_target`の誤用によるトークン窃取）であった点も重要で、依存パッケージそのものだけでなく、パッケージを公開する側のCI/CDパイプラインのセキュリティも同時に狙われるリスクを浮き彫りにした。この事案は、パッケージのインストール時点の検査だけでなく、パイプラインの権限管理と成果物の検証が同等に重要であることを示している。

## 実務での活用例

- 依存関係に`@asyncapi/specs`（6.11.2-alpha.1・6.11.2）、`@asyncapi/generator@3.3.1`、`@asyncapi/generator-components@0.7.1`、`@asyncapi/generator-helpers@1.1.1`が含まれていないか棚卸しし、含まれていれば既知の安全なバージョン（例：`@asyncapi/specs@6.11.1`以前）へピン留めする。
- 自社の依存パッケージにおいて、`--ignore-scripts`のみに依存したセキュリティ運用になっていないかを見直し、SCA（Software Composition Analysis）ツールによる継続的なパッケージ内容の検証や、依存関係のロックファイル差分レビューを併用する。
- 自組織が公開しているnpmパッケージのCI/CDワークフロー（特にGitHub Actionsの`pull_request_target`トリガー）を点検し、外部からのPRによってシークレットやトークンが奪取されうる構成になっていないか確認する。
- IPFSパブリックゲートウェイへのアウトバウンド通信など、通常の開発・ビルド環境では発生しにくい通信パターンをネットワーク境界で検知・遮断する仕組みを検討する。

## 注意点

- import時に実行されるマルウェアは、パッケージを`npm install`しただけでは発火せず、実際にコードから読み込んで初めて動作するため、インストールログの監視だけでは検知が難しい。
- 攻撃者は正規のnpm公開フローと自動化されたCI/CDパイプラインを悪用しており、パッケージの見た目や公開元の信頼性だけでは安全性を判断できない。
- 本記事の内容は2026年7月15日時点でMicrosoftが公開した分析に基づくものであり、影響範囲や対応状況は今後変わる可能性があるため、一次情報の継続的な確認が推奨される。

## 参考リンク

- [Unpacking the AsyncAPI npm supply chain compromise and import-time payload delivery | Microsoft Security Blog](https://www.microsoft.com/en-us/security/blog/2026/07/15/unpacking-asyncapi-npm-supply-chain-compromise-import-time-payload-delivery/)
