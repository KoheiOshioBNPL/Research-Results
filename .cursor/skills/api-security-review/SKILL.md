---
name: api-security-review
description: >-
  APIの実装・改修・レビューで、認証と認可の抜けを「ここを直す」という指摘として返す。
  Use when writing, editing, or reviewing HTTP, REST, GraphQL, gRPC, or webhook
  handlers, routes, controllers, resolvers, DTOs, serializers, or outbound API
  clients. Also use when the user asks for an API security review, OWASP API
  Top 10, BOLA, IDOR, mass assignment, or missing authz. Do not use for
  unrelated copy, papers, or refactors that do not touch an API boundary.
icon: shield
color: blue
---

# APIセキュリティ指摘

HTTPで資源や処理を出す入口を、書いた直後にレビューする。REST以外（GraphQL、gRPC、webhook、外向きのAPIクライアント）も同じ。

攻撃リクエスト、トークンの偽造、URLの細工、回避手順は書かない。欠けている検査と、サーバー側の直し方だけを返す。

## いつ使う

使う:

- ルート、コントローラ、リゾルバ、ハンドラ、DTO、シリアライザを追加または変更する
- 認証ミドルウェア、権限、レスポンスの形、更新ボディを触る
- ユーザーがAPIのセキュリティレビューを頼んだ

使わない:

- APIの入口を含まない変更
- 脆弱性の再現や攻撃用リクエストを求められたとき。指摘と防御の修正で止める

## 手順

1. `references/checklist.md` を読む。
2. 差分だけでなく、そのハンドラが通る認証と認可の実装を読む。差分に検査が無いことと、検査が無いことは別である。
3. 触った入口を列挙する。メソッド、パス、主体、対象の資源、操作。
4. 入口ごとにチェックリストを当てる。コードに根拠がなければ「未確認」にする。推測で欠陥を作らない。
5. 下の型で返す。実装依頼なら、「先に直す」は弱い版を残さず直す。レビューだけの依頼ならファイルは変えない。

## 指摘の型

ユーザーの言語に合わせる。各指摘は次の4つ。

- 場所: `file` の関数、または `METHOD /path`
- 分類: チェックリストのID
- 指摘: 何が欠けているか。1文
- 直し方: サーバーに足す検査。分かっているフィールド名を使う

並べ順は「先に直す」「一緒に直す」「未確認」。

該当がなければ、見た入口と、コード上で確認できた検査を1〜3行で書く。無言で通過しない。

## 判断

認証は誰か。認可は、その相手がこの対象にこの操作をしてよいか。ログイン済みは、資源に触ってよい理由にならない。

検査はサーバーに置く。画面から隠すこと、URLを秘密にすること、管理画面にリンクが無いことは検査ではない。

他人の資源は返さない。存在を隠すなら 404、権限を伝えるなら 403。既存の方針があればそれに合わせる。

## 実装中

同じ欠落を書こうとしたら、弱い実装を残さない。返信では直した場所を、上の型で残す。
