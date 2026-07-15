# Jscrambler npm パッケージ改ざんによるサプライチェーン攻撃

## 概要

2026年7月11日、コード難読化ツールとして広く使われている npm パッケージ「jscrambler」および関連プラグイン（jscrambler-webpack-plugin、gulp-jscrambler、grunt-jscrambler、jscrambler-metro-plugin など）が、盗まれた publish 権限を悪用され改ざんされました。悪意あるバージョン（jscrambler@8.14.0 など複数）には、インストール時に自動実行される難読化済みのネイティブバイナリが仕込まれており、開発者の端末や CI/CD 環境から認証情報を窃取する情報窃取マルウェアとして機能しました。セキュリティベンダーの Socket は公開からわずか6分でこの不正リリースを検知し、Jscrambler 側も速やかに該当バージョンを廃止して修正版をリリースしています。

## 背景

jscrambler は週間ダウンロード数が1万5千件を超える、実務でも利用されるコード保護・難読化ツールです。今回の攻撃では、攻撃者がパッケージ本体を新規に開発する必要はなく、正規の公開者アカウントの認証情報を奪取し、既存の信頼されたパッケージに悪意あるコードを混入させる手法が取られました。悪意あるコードは `preinstall` フックを通じて実行されるため、単に `npm install` を実行しただけで被害端末上でマルウェアが起動する仕組みになっていました。これは2026年に入ってから相次いでいる npm エコシステムを狙ったサプライチェーン攻撃の一つであり、同時期には npm 自体もインストールスクリプトを既定でブロックする npm v12 のリリースを控えていました。

## なぜ重要なのか

このインシデントが重要なのは、攻撃対象が「怪しい新規パッケージ」ではなく、多くのプロジェクトが日常的に信頼して使っている既存のセキュリティ関連ツールだった点です。難読化されたネイティブバイナリはプラットフォームを判別したうえで Linux・macOS・Windows 向けの実行ファイルをそれぞれ復元し、一時ディレクトリに隠し配置してバックグラウンドプロセスとして起動するという、検知回避を意識した高度な手口が用いられました。窃取対象には AWS・GCP・Azure などのクラウド認証情報、MetaMask や Phantom などの暗号資産ウォレット、さらには Claude Desktop、Cursor、Windsurf、VS Code といった AI コーディングアシスタントの資格情報まで含まれており、開発ワークフロー全体がリスクにさらされる時代であることを示しています。

## 実務での活用例

- 依存パッケージの `postinstall` / `preinstall` スクリプトを既定で無効化する（`npm config set ignore-scripts true` や npm v12 の新しいデフォルト挙動を活用する）。
- lockfile のハッシュ検証や Socket・Snyk などのサプライチェーン監視ツールを CI に組み込み、新規公開バージョンの異常なふるまい（バイナリ同梱、難読化コードなど）を自動検知する。
- 依存パッケージのアップデートを自動マージせず、公開直後のバージョンには一定のクールダウン期間を設けてから追随する運用ルールを設ける。
- CI/CD のビルド環境をクラウド認証情報の長期保持型から一時的なトークン発行型に切り替え、万が一の侵害時の被害範囲を最小化する。

## 注意点

該当パッケージ（jscrambler 8.14.0 / 8.16.0 / 8.17.0 / 8.18.0 / 8.20.0、jscrambler-webpack-plugin 8.6.2、gulp-jscrambler 8.6.2、grunt-jscrambler 8.5.2、jscrambler-metro-plugin 9.0.2 など）をインストール・使用した端末や CI 環境では、単なるパッケージの差し替えだけでなく、クラウド認証情報やウォレットの秘密鍵、AI ツールのトークンなど既に窃取された可能性のある資格情報の総点検・失効・再発行が必須です。パッケージが「有名」「セキュリティ関連」であることは安全の保証にはならず、今回のように防御ツール自体が攻撃の踏み台になり得る点を踏まえ、サプライチェーン全体を継続的に監視する体制が求められます。

## 参考リンク

- [jscrambler npm Package Compromised in Supply Chain Attack - Socket](https://socket.dev/blog/jscrambler-supply-chain-attack)
- [Compromised jscrambler 8.14.0 npm Release Drops Rust Infostealer During Install - The Hacker News](https://thehackernews.com/2026/07/compromised-jscrambler-8140-npm-release.html)
- [Active Exploitation Alert: Jscrambler npm Packages Compromised in Coordinated Supply Chain Attack (July 2026) – Rescana](https://www.rescana.com/post/active-exploitation-alert-jscrambler-npm-packages-compromised-in-coordinated-supply-chain-attack-july-2026)
- [Jscrambler npm Supply Chain Attack Steals Developer and Cloud Credentials - Cyberpress](https://cyberpress.org/jscrambler-npm-supply-chain-attack/)
- [Multiple Jscrambler Packages Impacted by Supply Chain Attack - SecurityWeek](https://www.securityweek.com/multiple-jscrambler-packages-impacted-by-supply-chain-attack/)
