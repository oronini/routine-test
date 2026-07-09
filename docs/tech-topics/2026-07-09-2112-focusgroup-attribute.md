# HTML `focusgroup` 属性：JavaScriptなしで実現するコンポジットウィジェットのキーボード操作

## 概要

2026年7月2日にリリースされたMicrosoft Edge 150において、HTMLの新しい属性`focusgroup`が早期テスト機能として実装された。`focusgroup`は、ツールバーやタブリスト、メニュー、リストボックスなどの「コンポジットウィジェット（複合UIコンポーネント）」に対して、矢印キーによるフォーカス移動を宣言的に付与するための仕組みである。これまでJavaScriptで実装する必要があった「ローミングtabindex」パターンを、HTML属性ひとつで代替できる点が特徴である。Microsoftが試作しOpen UIコミュニティグループで仕様策定が進められてきたもので、現在Chromium系ブラウザで先行的に利用可能になっている。

## 背景

WAI-ARIAのAuthoring Practices Guide（APG）は、ツールバーやタブ、メニューといった複合ウィジェットにおいて、Tabキーではなく矢印キーで要素間を移動できるようにすることを推奨してきた。この挙動を実現するには、通常は各子要素の`tabindex`を動的に`0`と`-1`で切り替える「ローミングtabindex」というJavaScriptパターンが必要であり、フォーカス管理・イベントハンドリング・ラップアラウンド（端での折り返し）などを開発者自身が実装しなければならなかった。この実装の複雑さと、実装ごとの品質のばらつきが、アクセシブルなカスタムUIコンポーネントを作る際の障壁となっていた。`focusgroup`属性は、この定型的なロジックをブラウザ側の標準機能として提供することで、開発者の負担を軽減しようとする提案である。

## なぜ重要なのか

`focusgroup`はブラウザネイティブの機能であるため、アクセシビリティ対応のための独自JavaScriptの量を減らせるだけでなく、実装差異によるバグや挙動の不整合も抑えられる。属性値は`focusgroup="<behavior> [inline|block] [wrap] [nomemory]"`という構文を取り、`toolbar`・`tablist`・`radiogroup`・`listbox`・`menu`・`menubar`などの挙動トークンで対象のウィジェットパターンを宣言できる。`inline`/`block`で矢印キーの移動軸を限定し、`wrap`で端でのフォーカス折り返しを有効化するなど、細かい制御も可能である。デザインシステムやUIライブラリを開発・利用する立場から見ると、独自実装していたキーボード操作ロジックを段階的にHTML標準機能へ置き換えられる可能性があり、保守コストの削減とアクセシビリティ品質の底上げの両方に寄与する点が重要である。

## 実務での活用例

例えばカスタムツールバーコンポーネントを実装する場合、これまでは各ボタンに`tabindex`を動的に付け替え、`keydown`イベントで矢印キーを検知してフォーカスを移動させるロジックを自前で書く必要があった。`focusgroup`を使えば、親要素に`focusgroup="toolbar inline wrap"`を指定するだけで、Tab移動は1回のみ、その後は左右矢印キーで子要素間を巡回できるようになる。同様にタブUIでは`focusgroup="tablist inline"`、縦方向のメニューでは`focusgroup="menu block wrap"`のように指定することで、WAI-ARIA APGが推奨するキーボード操作パターンをマークアップだけで再現できる。デザインシステムのコンポーネントライブラリにこの属性を組み込んでおけば、利用者側は個別にキーボードハンドリングを書かずに済む。

## 注意点

2026年7月時点では、`focusgroup`はChromium系ブラウザでの早期テスト段階の機能であり、仕様自体もOpen UIコミュニティグループおよびWHATWG HTML標準での議論が継続中である。Safari・Firefoxを含む全ブラウザでの安定サポートはまだ確立されていないため、本番環境で唯一の実装手段として採用するのはリスクがある。対応していないブラウザ向けには、従来のローミングtabindexによるフォールバック実装を用意するか、機能検出を行った上で段階的に導入する必要がある。また、`focusgroup`はあくまでキーボードのフォーカス移動を扱うものであり、`role`属性や`aria-*`属性によるセマンティクスの付与は別途必要である点にも注意したい。

## 参考リンク

- [Request for developer feedback: focusgroup | Blog | Chrome for Developers](https://developer.chrome.com/blog/focusgroup-rfc)
- [Focusgroup (Explainer) | Open UI](https://open-ui.org/components/scoped-focusgroup.explainer/)
- [[focusgroup] Declarative directional focus navigation for composite widgets · Issue #11641 · whatwg/html](https://github.com/whatwg/html/issues/11641)
- [Microsoft Edge 150 web platform release notes (Jul. 2026) - Microsoft Edge Developer documentation](https://learn.microsoft.com/en-us/microsoft-edge/web-platform/release-notes/150)
- [Focusgroup: Interactive Demos](https://microsoftedge.github.io/Demos/focusgroup/)
