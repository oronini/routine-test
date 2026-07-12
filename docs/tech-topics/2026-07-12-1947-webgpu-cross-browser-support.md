# WebGPUが主要ブラウザで出揃う ― 15年続いたWebGLの時代に区切り

## 概要

WebGPUがChrome・Edge・Firefox・Safariの主要4ブラウザすべてで正式サポートされ、GPUを直接活用した高性能な描画・計算をブラウザ上で行うための標準APIとして実質的な普及フェーズに入った。W3CのGPU for the Web Working Groupによる長年の共同開発の成果であり、Apple・Google・Intel・Microsoft・Mozillaといった競合各社が足並みを揃えてきた点が特徴的である。Windows・macOS・ChromeOSに加えAndroidや各社のモバイルOSでも有効化が進んでおり、長らくウェブの3D・GPU計算APIの事実上の標準であったWebGLから、WebGPUへの移行が現実的な選択肢になりつつある。

## 背景

WebGLは2011年の登場以来、ブラウザ上での3D描画やGPUを用いた汎用計算（GPGPU）を支えてきたが、OpenGL ES を土台とする設計のため、現代のGPUアーキテクチャが持つ並列計算能力やコンピュートシェーダーを十分に活用できないという制約があった。WebGPUはこの制約を解消するために、Vulkan・Metal・Direct3D 12といった最新の低レベルGPU APIを土台として一から設計された新しい標準である。Chrome・Edgeはバージョン113から対応を開始し、その後Android版（Chrome 121以降）、Firefox（Windows版はバージョン141以降）、SafariもmacOS Tahoe・iOS/iPadOS 26以降でデフォルト有効化するなど、各ブラウザベンダーが段階的に足並みを揃えてきた。現時点での残課題はLinux環境で、Firefoxはnightly版のみの対応にとどまり、Chromeもドライバ単位で段階的に展開している状況である。

## なぜ重要なのか

WebGPUの普及は、ブラウザ上で動くアプリケーションの性能上限を大きく引き上げる。コンピュートシェーダーを用いた汎用GPU計算がネイティブに近い形で使えるようになることで、ブラウザ内での機械学習推論（TensorFlow.jsやONNX Runtime Webなど）、大規模データの可視化、CADやシミュレーション、リアルタイム画像処理といった、従来サーバーサイドやネイティブアプリに頼らざるを得なかった処理をクライアントサイドで完結できる可能性が広がる。特に近年注目される「ブラウザ内でのLLM推論・画像生成」といったAI関連のクライアントサイド処理は、WebGPUの計算能力を前提に設計されるケースが増えており、Web標準の進化がAI活用の裾野を広げるという点でも意義が大きい。またWebGLからの単純な移行ではなく、シェーダー言語（WGSL）やAPI設計思想が刷新されているため、フロントエンド開発者にとって新たに習得すべき技術領域としても注目度が高い。

## 実務での活用例

- Three.js・Babylon.jsなど主要な3Dライブラリが提供するWebGPUレンダラーを試験導入し、既存のWebGL実装からの描画性能・互換性を比較検証する。
- TensorFlow.jsやONNX Runtime WebのWebGPUバックエンドを用いて、ブラウザ内での画像認識・音声処理モデルの推論速度を計測し、サーバーサイド推論からの置き換えが可能かを検討する。
- `navigator.gpu`の存在確認によるフィーチャーディテクションを実装し、WebGPU未対応環境（特にLinux）向けにWebGLへのフォールバックパスを用意する。
- 大規模データセットの可視化ダッシュボードなど、GPUの並列計算が効くユースケースについて、WebGPUのコンピュートシェーダーを用いたプロトタイプを作成し、既存実装とのパフォーマンス差を社内向けに検証・共有する。

## 注意点

- Linux環境でのサポートはまだ発展途上であり、Firefoxは安定版で未対応、Chromeもドライバ依存で挙動が揺れるため、対象ユーザー層にLinuxデスクトップが多い場合は本番投入前に十分な検証が必要である。
- WebGLとはAPI設計・シェーダー言語（WGSLとGLSL）が大きく異なるため、既存WebGL資産の移行には単純な書き換え以上の設計見直しが伴う。
- GPUを直接操作する性質上、ドライバやハードウェアの差異によって挙動が変わりうるため、対応デバイス・OSのマトリクスを明確にした上でのクロスブラウザ・クロスデバイステストが従来以上に重要になる。

## 参考リンク

- [WebGPU is now supported in major browsers | Blog | web.dev](https://web.dev/blog/webgpu-supported-major-browsers)
- [WebGPU API - MDN Web Docs - Mozilla](https://developer.mozilla.org/en-US/docs/Web/API/WebGPU_API)
- [WebGPU | Can I use... Support tables for HTML5, CSS3, etc](https://caniuse.com/webgpu)
