# Soft Navigations API による SPA の Core Web Vitals 計測

## 概要

Chrome 151（2026年7月リリース）にて、「Soft Navigations API」がフラグなしで全サイトに向けて利用可能になった。これはシングルページアプリケーション（SPA）における画面遷移（ソフトナビゲーション）を検出し、Core Web Vitals（LCP・CLS・INP）を遷移ごとに正しく計測できるようにするための新しいWeb標準APIである。

## 背景

従来のCore Web Vitalsは「ハードナビゲーション」（ページの初回読み込み）を前提に設計されていた。しかしSPAでは、ユーザーがリンクやボタンをクリックしてもURLが変わるだけでページ全体の再読み込みは発生しない「ソフトナビゲーション」が多用される。この場合、LCPは初回読み込み時にしか計測されず、CLSやINPはセッション全体で累積され、最初に読み込まれたURLに紐づけられてしまうため、実際のユーザー体験を正しく反映しない計測結果になっていた。

この課題を解決するため、Chromeチームは2026年3月（Chrome 147）からOrigin Trialを開始し、2026年7月のChrome 151で正式に有効化するに至った。

## なぜ重要なのか

- SPAは「ユーザー操作によって開始される」「URLが視覚的に変化する」「ペイントが発生する」という条件を満たす遷移を「ソフトナビゲーション」として定義し、`soft-navigation`エントリとして観測できるようになる。
- これにより、LCPは初回読み込み時の`largest-contentful-paint`に加え、ソフトナビゲーション後の`interaction-contentful-paint`でも計測可能になる。
- CLS・INPも`layout-shift`・`event`エントリを`soft-navigation`エントリの区切りでスライスして計測できるようになり、遷移ごとの体験品質を正確に把握できる。
- Reactやその他のSPAフレームワークを採用するWebサイトにとって、これまで「計測できない」「不正確」だったCore Web Vitalsが、実際のユーザー体験に即した形でSEO評価やパフォーマンス改善の指標として使えるようになる。

## 実務での活用例

- RUM（Real User Monitoring）ツールやアナリティクス基盤に`PerformanceObserver`で`soft-navigation`エントリを監視する処理を追加し、ページ遷移単位でのCore Web Vitalsダッシュボードを構築する。
- 既存のSPAで「初回読み込みは速いが、2回目以降の画面遷移が遅い」といった問題を、遷移単位のLCP/INPデータをもとに特定・改善する。
- Lighthouse CIやSearch Console等のツールがSoft Navigations対応を進めた場合に備え、自社のCore Web Vitalsレポート運用フローを見直しておく。

## 注意点

- 対応しているのは現時点でChromiumベースのブラウザ（Chrome）が中心であり、他ブラウザでの対応状況は異なるため、クロスブラウザでの計測には従来の代替手段との併用を検討する必要がある。
- 「ソフトナビゲーション」の判定はヒューリスティックに基づくため、URL変更を伴わないUI更新やペイントを伴わない遷移など、意図と異なる検出・非検出が発生する可能性がある。
- 既存のRUM基盤やパフォーマンス計測コードが「ハードナビゲーション前提」で設計されている場合、集計ロジックの見直しが必要になる。

## 参考リンク

- [Measuring soft navigations | Web Platform | Chrome for Developers](https://developer.chrome.com/docs/web-platform/soft-navigations)
- [Final Soft Navigations origin trial starting in Chrome 147 | Blog | Chrome for Developers](https://developer.chrome.com/blog/final-soft-navigations-origin-trial)
- [New Soft Navigations origin trial | Blog | Chrome for Developers](https://developer.chrome.com/blog/new-soft-navigations-origin-trial)
