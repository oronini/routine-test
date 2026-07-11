# Vitest 5への布石 ― clearMocksデフォルト化とマルチランタイム対応の議論

## 概要

Vite/Vitestの開発元VoidZeroは、Vitest 4系の安定運用と並行して次期メジャーバージョン「Vitest 5」に向けた設計議論を公開の場（GitHub Discussions）で進めている。現時点ではベータ版（v5.0.0-beta.x系）が段階的に公開されており、`clearMocks`のデフォルト有効化、ブラウザモードにおけるロケーターの`exact`マッチングのデフォルト化、`toHaveTextContent`の厳密一致化など、テストの堅牢性を高める破壊的変更が検討されている。あわせて、Node・Deno・Bunといった複数ランタイムで同一テストを実行できるマルチランタイム対応や、Playwrightライクなロケーターピッカーの実装なども要望として挙がっている。

## 背景

Vitest 4.0はブラウザモード（Browser Mode）が正式に安定版となり、JSDOMのような擬似DOM環境ではなく実ブラウザ上でコンポーネントテストを実行できるようになったことが大きな節目だった。その後4.1系では、CDP（Chrome DevTools Protocol）アクセス制限まわりの修正やPlaywrightルートの孤立防止など、ブラウザモードを中心とした細かな安定化が続いている。

一方でメンテナンスチームは、Vite 8のリリースに合わせてVitest 4.1でVite 8互換を確保する方針を示しつつ、次のメジャーバージョンであるVitest 5については「厳密なタイムライン は設けない」とし、Viteのリリースサイクルに合わせて必要な破壊的変更をまとめて導入する姿勢を取っている。GitHub Discussions #9664では、コミュニティから寄せられた要望や懸念を踏まえ、何をVitest 5の破壊的変更として取り込むかが継続的に議論されている。

## なぜ重要なのか

テストランナーのデフォルト挙動の変更は、CIパイプライン全体に影響する。特に`clearMocks`のデフォルト有効化は、モックの状態がテスト間で意図せず引き継がれることによる「順序依存のflakyテスト」を減らす一方、既存のテストスイートでモッククリアを前提にしたコードが動作しなくなる可能性があり、移行時の影響範囲が大きい。

また、ブラウザモードのロケーターで`exact: true`がデフォルトになると、部分一致に依存していたセレクタが壊れる可能性がある。これはPlaywrightやTesting Libraryのベストプラクティスに寄せる方向性であり、テストの脆弱性（DOM構造のわずかな変化で壊れる問題）を減らす狙いがある。マルチランタイム対応が実現すれば、Node.js専用パッケージとEdge/Deno/Bunランタイム向けパッケージを同一のテストコードで検証できるようになり、近年のマルチランタイム化するJavaScriptエコシステムにおいてテスト基盤の重複を解消できる。

## 実務での活用例

- 既存プロジェクトでVitest 4.1を使っている場合、`beforeEach`で明示的に`vi.clearAllMocks()`を呼んでいる箇所を洗い出し、Vitest 5移行時に重複呼び出しにならないか事前に確認しておく。
- ブラウザモードでテキストベースのアサーション（`toHaveTextContent`など）を多用しているプロジェクトは、厳密一致化に備えて部分一致が必要なケースを`toMatchTextContent`（提案中のAPI）に置き換える設計を検討する。
- Cloudflare Workers・Deno Deploy・Bunなど複数ランタイムをターゲットにしたライブラリを開発している場合、Vitestのprojects機能を使ったマルチランタイムテスト構成を早めに試し、Vitest 5のロードマップにフィードバックする。
- CI環境ではVite 8への追従状況を踏まえ、Vitestのアップグレードタイミングをフレームワーク側（Next.js、Nuxt、TanStack Startなど）のVite 8対応リリースと合わせて計画する。

## 注意点

- 本記事執筆時点でVitest 5は正式リリースされておらず、ベータ版の仕様は今後変更される可能性がある。破壊的変更の内容は議論の進行によって覆ることもあるため、移行作業に着手する前に必ず公式のマイグレーションガイドを確認すること。
- `clearMocks`や`exact`ロケーターマッチングのデフォルト変更は、既存テストのサイレントな失敗（誤ったパスでテストが「通ってしまう」）ではなく、想定通り「落ちる」形で顕在化することが多いため、CIで早期に検知できるよう段階的に導入するのが望ましい。
- `fsModuleCache`、`openTelemetry`、`importDurations`、`viteModuleRunner`などの実験的機能はまだ安定化前であり、本番のCIパイプラインで無条件に有効化することは推奨されない。

## 参考リンク

- [Vitest 5 · vitest-dev/vitest · Discussion #9664](https://github.com/vitest-dev/vitest/discussions/9664)
- [Releases · vitest-dev/vitest](https://github.com/vitest-dev/vitest/releases)
- [vitest - npm (バージョン一覧)](https://www.npmjs.com/package/vitest?activeTab=versions)
