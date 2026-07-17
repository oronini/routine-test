# Supabase Realtime Broadcastのバイナリペイロード対応

## 概要

2026年7月、Supabaseは「Developer Update - July 2026」の中で、Realtime Broadcast機能がJSONに加えてバイナリペイロード（ArrayBufferやUint8Array等）の送受信に対応したことを発表した。クライアントライブラリ経由のWebSocket通信、REST API、データベースから直接Broadcastする方式のいずれでもバイナリデータを扱えるようになり、JSONのテキストエンコーディングに伴うオーバーヘッドを回避できるようになった。

## 背景

SupabaseのRealtime Broadcastは、これまでJSON形式でのみメッセージを送受信していた。数値主体・高頻度・高密度なデータをやり取りする場合、JSONのテキストエンコーディングは冗長になりやすく、たとえば1つのセンサー値がJSONでは20〜30文字を要するのに対し、バイナリでは数バイトに収まるケースも多い。IoTデバイスからのテレメトリ配信や、ライブスクリーンショット・画像フレームの配信のようなユースケースが増える中で、エンコード効率とスループットの改善が求められていた。この要望に応える形で、Broadcastのペイロード層にバイナリ対応が追加された。

## なぜ重要なのか

バイナリペイロード対応により、数千台規模のIoTデバイスからのセンサーストリームや、加速度センサー値・GPS座標・バッテリー残量といった数値データを固定長のバイナリフィールドに詰め込んで配信できるようになり、帯域幅とサーバー負荷を大きく削減できる。またサポートエージェントによる画面共有のようにJPEG/PNGの画像フレームを周期的に配信するユースケースでも、Base64エンコードなどを介さずに直接バイナリを送受信できるため、レイテンシとCPUオーバーヘッドの両面でメリットがある。リアルタイム通信基盤がJSON専用からバイナリ対応へ拡張されたことは、Supabase RealtimeをIoTやメディアストリーミングのような高スループット用途にも適用しやすくする意味で重要な変化といえる。

## 実務での活用例

- IoTデバイスのテレメトリ収集基盤で、センサー値を固定長バイナリ形式にパックしてBroadcastし、JSONと比較したペイロードサイズ・帯域幅の削減効果を検証する。
- サポートチャットや画面共有機能において、キャプチャした画像フレームをBase64エンコードせずバイナリのままBroadcastし、エンコード・デコードのCPUコストを削減する。
- 既存のJSONベースのBroadcast実装からバイナリ形式への移行を検討する際、supabase-js（2.91.0以降）やsupabase-swift（2.44.0以降）など、クライアントSDKのバージョン対応状況を事前に確認する。
- データベースから直接Broadcastする既存の仕組み（Realtime Broadcast from Database）と組み合わせ、数値主体のイベントデータをバイナリ形式で配信するパイプラインを設計する。

## 注意点

- バイナリペイロードは対応済みのSDKバージョン（supabase-js 2.91.0以降、supabase-swift 2.44.0以降など）でのみ利用可能であり、Dart・Kotlin・Python向けクライアントは本稿執筆時点で未対応である。
- 対応していない旧バージョンのSDKを使うクライアントにバイナリペイロードを送信した場合、メッセージが警告なく破棄（サイレントドロップ）される点に注意が必要であり、混在環境では受信側の対応状況を事前に統一しておく必要がある。
- バイナリ形式はデバッグ時の可読性がJSONに劣るため、運用時のロギングやトラブルシューティングの仕組みを別途整備しておくことが望ましい。

## 参考リンク

- [Developer Update - July 2026 · Changelog](https://supabase.com/changelog/47796-developer-update-july-2026)
- [Broadcast | Supabase Docs](https://supabase.com/docs/guides/realtime/broadcast)
- [GitHub - supabase/realtime: Broadcast, Presence, and Postgres Changes via WebSockets](https://github.com/supabase/realtime)
