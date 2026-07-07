# React Server Components の重大リモートコード実行脆弱性（CVE-2025-55182）

## 概要

2025年12月、React Server Components (RSC) が利用する内部シリアライズ形式「Flight プロトコル」に、CVSSスコア10.0（最高値）と評価される重大なリモートコード実行（RCE）脆弱性が発見されました。CVE-2025-55182として採番され、通称「React2Shell」とも呼ばれています。React 19系（19.0、19.1.0、19.1.1、19.2.0など）、およびApp Routerを利用するNext.js 15.x/16.x系に影響し、修正版としてReact 19.0.1・19.1.2・19.2.1、Next.js 15.0.5・15.1.9・15.2.6・15.3.6・15.4.8・15.5.7・16.0.7などが公開されています。

## 背景

React Server Componentsは、サーバー側でレンダリングした結果をクライアントに送るための独自バイナリ風プロトコル（Flight）を使ってデータをやり取りしています。このプロトコルは本来サーバーとクライアント間の信頼された通信を前提に設計されていましたが、悪意あるペイロードを含むPOSTリクエストが送られた際に、データ構造の検証が不十分なままデシリアライズ処理が行われることが判明しました。結果として、攻撃者が細工したデータがサーバー側の実行ロジックに影響を与え、認証なしで任意コードを実行できる状態になっていました。

## なぜ重要なのか

CVSS 10.0という満点評価は、認証不要・攻撃条件が容易・影響範囲が甚大であることを意味します。実際に脆弱性の公開後、CISAのKEV（Known Exploited Vulnerabilities）カタログに追加されるほど活発に悪用され、トンネリングツールやダウンローダー、バックドア、さらには暗号資産マイニングマルウェアの設置など、日和見的なサイバー犯罪グループから高度な脅威アクターまで幅広く悪用が確認されました。Reactは開発者の約4割が利用し、Next.jsもその中で大きなシェアを持つため、推定1,200万以上のWebサイトが影響を受けた可能性があるとされ、フロントエンドフレームワークの脆弱性が単なるUIバグではなくインフラ全体のセキュリティ問題になり得ることを改めて示した事例です。

## 実務での活用例

- 自社プロダクトで使用しているReact・Next.jsのバージョンを棚卸しし、修正版（React 19.0.1/19.1.2/19.2.1以降、Next.js 15.0.5/15.1.9/15.2.6/15.3.6/15.4.8/15.5.7/16.0.7以降）へ速やかにアップグレードする。
- App Routerを採用しているプロジェクトでは、CI/CDパイプラインに依存パッケージの脆弱性スキャンを組み込み、同種のサプライチェーン型脆弱性を早期検知できる体制を整える。
- WAF（Web Application Firewall）やリバースプロキシ層で、RSCのFlightプロトコル向けエンドポイントに対する異常なペイロードを検知・遮断するルールを追加する。
- インシデント対応の一環として、該当バージョンを利用していた期間のアクセスログを遡って確認し、不審なPOSTリクエストや外部通信がないかを調査する。

## 注意点

- パッチ適用だけでなく、既に侵害されていないかの事後調査（マルウェア設置やバックドアの有無）も併せて実施する必要があります。
- Next.jsのマイナーバージョンごとに修正版の番号が異なるため、自社が使用している具体的なバージョン系列を確認したうえで、該当する修正版以上にアップグレードすることが重要です。
- 本記事の情報は執筆時点の公開情報に基づくものであり、今後追加の調査で影響範囲や攻撃手法の詳細が更新される可能性があるため、各ベンダーの公式アドバイザリを継続的に確認してください。

## 参考リンク

- [Exploitation of Critical Vulnerability in React Server Components - Unit 42 (Palo Alto Networks)](https://unit42.paloaltonetworks.com/cve-2025-55182-react-and-cve-2025-66478-next/)
- [Critical Security Vulnerability in React Server Components - React公式ブログ](https://react.dev/blog/2025/12/03/critical-security-vulnerability-in-react-server-components)
- [RCE in React Server Components - vercel/next.js Security Advisory (GHSA-9qr9-h5gf-34mp)](https://github.com/vercel/next.js/security/advisories/GHSA-9qr9-h5gf-34mp)
- [Defending against the CVE-2025-55182 (React2Shell) vulnerability - Microsoft Security Blog](https://www.microsoft.com/en-us/security/blog/2025/12/15/defending-against-the-cve-2025-55182-react2shell-vulnerability-in-react-server-components/)
