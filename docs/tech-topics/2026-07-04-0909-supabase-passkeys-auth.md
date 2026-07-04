# Supabase Authにパスキー（WebAuthn）ログインがベータ提供開始

## 概要

2026年6月、SupabaseはSupabase Authにおけるパスキー（Passkeys）機能をベータ版として発表した。これはWebAuthn標準に基づくパスワードレスかつフィッシング耐性を持つ認証方式であり、すべてのプロジェクトで今日からベータ利用が可能になっている。ユーザーはFace IDやTouch ID、Windows Helloといった生体認証、端末のPINコード、あるいはハードウェアセキュリティキーを用いてサインインできるようになった。

## 背景

これまでSupabase Authはメール・パスワード認証やOAuth、マジックリンクなどを中心にサポートしてきたが、パスワード漏えいやフィッシング攻撃への対策として、業界全体でパスキーへの移行が進んでいる。Supabaseコミュニティでは以前からWebAuthn対応を求める議論が続いており、今回のベータリリースはその要望に応える形となった。パスキー機能を利用するには`@supabase/supabase-js`のv2.105.0以降、あるいは対応するFlutter・Swift SDKの新しいバージョンが必要とされる。

## なぜ重要なのか

パスキーはWebAuthn標準に準拠しており、秘密鍵は常にユーザーのデバイス上に留まり、Supabase Auth側は検証用の公開鍵のみを保管する。これにより、サーバー側のデータ漏えいが起きてもクレデンシャル自体が流出するリスクを構造的に低減できる。また、パスワードの入力や記憶が不要になるため、フィッシングサイトに認証情報をだまし取られる攻撃そのものが成立しにくくなる。パスワード管理の負担軽減とセキュリティ強化を同時に実現できる点で、認証まわりの実務における意味は大きい。

## 実務での活用例

登録・ログインの流れはいずれもWebAuthnの3ステップ（Options・Ceremony・Verify）で構成される。まずクライアントがSupabase Authにチャレンジを要求し（Options）、次にブラウザが`navigator.credentials.create()`や`navigator.credentials.get()`を呼び出して生体認証やセキュリティキーによる認証を実行し（Ceremony）、最後に署名済みのレスポンスをSupabase Authに送信して検証する（Verify）という流れになる。導入時はダッシュボードの「Authentication → Passkeys」からパスキー認証を有効化し、Relying Party Display Name・Relying Party ID（ドメイン）・Relying Party OriginsといったWebAuthnの設定値を入力するだけでよく、既存のメール・パスワード認証と併用してオプトイン的に導入できる。

## 注意点

Passkeys APIは現時点では実験的（experimental）な位置づけであり、ベータ期間中は予告なく仕様が変更される可能性があるため利用には明示的なオプトインが必要である。また対応するクライアントSDKのバージョンを満たしていないと利用できないため、既存プロジェクトでは事前にSDKのアップデートが必要になる。パスキーはあくまで追加の認証手段であり、既存ユーザー全体への移行やブラウザ・OS側のWebAuthn対応状況も踏まえて段階的に導入を検討するのが望ましい。

## 参考リンク

- [Passkeys for Supabase Auth (Beta) · Changelog](https://supabase.com/changelog/46458-passkeys-for-supabase-auth-beta)
- [Passkey authentication | Supabase Docs](https://supabase.com/docs/guides/auth/passkeys)
- [Developer Update - June 2026 · Changelog](https://supabase.com/changelog/46689-developer-update-june-2026)
