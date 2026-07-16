# Node.jsのTypeScript Type Strippingがデフォルトで有効に

## 概要

Node.js 22.18.0（LTS「Jod」）以降、TypeScriptファイルを`--experimental-strip-types`のような追加フラグなしで直接実行できる「Type Stripping」機能がデフォルトで有効になりました。`node file.ts`とコマンドを打つだけで、型注釈が取り除かれたTypeScriptコードがそのまま実行されます。

## 背景

これまでNode.jsでTypeScriptを実行するには、`ts-node`や`tsx`といった外部ツール、あるいは事前のトランスパイルビルドステップが必要でした。Node.jsチームはこの摩擦を減らすため、型注釈を実行時のコード変換なしに「取り除く（strip）」だけの軽量な仕組みを導入し、まず実験的フラグ付きで提供したのち、v22.18.0でデフォルト有効化に踏み切りました。内部的にはSWCエンジンをラップした`amaro`モジュールが使われており、型注釈は空白に置き換えられるため、行番号やスタックトレースの位置がずれない設計になっています。

## なぜ重要なのか

- ビルドステップなしでTypeScriptを直接実行できるため、スクリプトやCLIツール、簡易なNode.jsプロジェクトの開発体験が大幅に簡素化される。
- あくまで型情報の除去のみで型チェックは行わないため、既存のトランスパイラ（`tsc`、esbuild、SWCなど)を使ったビルドパイプラインを即座に置き換えるものではないという理解が必要。
- Node.js自体がTypeScriptをファーストクラスに近い形でサポートし始めたことは、TypeScriptがJavaScriptエコシステムの事実上の標準であることを改めて示している。

## 実務での活用例

- ちょっとしたスクリプトやNode.js製CLIツールを、ビルド設定なしに`.ts`のまま直接実行する。
- ローカル開発環境やCIの補助スクリプトをTypeScriptで書きつつ、余分な依存関係（`ts-node`など）を減らす。
- 本番運用のような型チェックが必須なプロジェクトでは、この機能を型チェックの代替とはせず、`tsc --noEmit`等の型検査を別途CIに組み込む運用を継続する。

## 注意点

- Type Strippingは型チェックを行わないため、型エラーがあっても実行時にはエラーにならない場合がある。
- サポートされる構文には制限があり、`enum`や名前空間などTypeScript固有の一部機能は変換が必要になることがある（詳細はNode.js公式ドキュメント参照）。
- 実験的機能として導入された経緯があり、`--no-experimental-strip-types`フラグで無効化も可能。プロジェクトのNode.jsバージョンによって挙動が異なる点に注意する。

## 参考リンク

- [Release 2025-07-31, Version 22.18.0 'Jod' (LTS) · nodejs/node](https://github.com/nodejs/node/releases/tag/v22.18.0)
- [Modules: TypeScript | Node.js Documentation](https://nodejs.org/api/typescript.html)
