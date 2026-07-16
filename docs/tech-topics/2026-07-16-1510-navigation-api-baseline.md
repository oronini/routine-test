# Navigation API が Baseline Newly Available に到達

## 概要

SPA（シングルページアプリケーション）のクライアントサイド遷移を扱うための新しいブラウザ標準API「Navigation API」が、主要ブラウザで足並みが揃い「Baseline Newly Available」のステータスに到達しました。Chrome、Edge、Firefox 147、Safari 26.2 でサポートされており、長年SPA開発者を悩ませてきた History API の課題を解決するものとして注目されています。

## 背景

これまでSPAでのルーティング実装では、リンククリックへの `click` イベントリスナー登録、`popstate` イベントでの戻る・進む操作のハンドリング、`history.pushState`/`replaceState` による履歴操作など、断片的な仕組みを開発者自身が組み合わせる必要がありました。フォーム送信やブラウザバックなど、遷移の種類ごとに挙動がばらつき、フォーカス管理やスクロール位置の復元といったアクセシビリティ関連の処理も個別に実装する必要がありました。

Navigation API は、この断片化した処理を単一の `navigate` イベントに統一することを目的として設計されました。リンククリック、フォーム送信、戻る・進むボタン、プログラムからの呼び出しなど、あらゆる種類のナビゲーションがこのイベント経由で捕捉できます。

## なぜ重要なのか

- **統一されたインターフェース**: すべてのナビゲーション種別を単一の `navigate` イベントで扱えるため、`click` や `popstate` を個別にハンドリングするコードが不要になります。
- **`intercept()` による宣言的な制御**: `event.intercept()` を呼ぶだけで、URL更新・履歴スタック管理・フォーカス移動といったアクセシビリティ関連の処理までブラウザ側が自動的に面倒を見てくれます。
- **フレームワーク非依存**: React Router や Next.js、Remix などのルーティングライブラリが内部実装として採用し始めており、今後はフレームワークを問わずブラウザネイティブな挙動に近づいていくことが期待されます。
- **Baseline到達の意味**: 「Newly Available」はすべての主要ブラウザで実装が完了したことを意味し、実務での採用を検討できる段階に入ったことを示します。

## 実務での活用例

- 独自のSPAルーティング層を自作している場合、`navigate` イベントと `intercept()` を使うことで、URL同期・履歴管理・スクロール復元のための独自コードを削減できます。
- `NavigationHistoryEntry` を利用すると、履歴エントリごとに任意の状態（state）を紐付けて保持できるため、`sessionStorage` を使った状態保持のワークアラウンドが不要になるケースがあります。
- 既存の `history.pushState` ベースの実装からの移行を検討する際は、まず読み取り専用の用途（現在の遷移種別の判定やロギング）から段階的に導入し、`intercept()` によるナビゲーション制御は影響範囲を確認しながら移行するのが安全です。

## 注意点

- Baseline Newly Available はサポート開始から日が浅いステータスであり、古いブラウザバージョンを利用するユーザー層が一定数いる場合はポリフィルや `history` API へのフォールバックを検討する必要があります。
- `intercept()` の挙動はブラウザごとに細かな差異が残っている可能性があるため、本番導入前にターゲットブラウザでの十分な検証が必要です。
- 既存のルーティングライブラリを使っている場合、ライブラリ側がNavigation APIへ対応するタイミングを待つ方が、自前実装より安全で保守コストも低くなることが多いです。

## 参考リンク

- [Navigation API - a better way to navigate, is now Baseline Newly Available | web.dev](https://web.dev/blog/baseline-navigation-api)
- [Navigation API - MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/API/Navigation_API)
- [Navigation API Reaches Baseline Newly Available as Replacement to the History API - InfoQ](https://www.infoq.com/news/2026/05/navigation-api-browser/)
