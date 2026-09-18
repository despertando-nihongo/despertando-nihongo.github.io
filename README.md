# デスペルタンド 日本語 — despertando-nihongo.com

光のメッセージを掲載しているブログの静的サイト版です。
[Hugo](https://gohugo.io/) でビルドし、GitHub Pages で公開しています。
`main` ブランチに push すると、GitHub Actions が自動でビルド・公開します（数分かかります）。

## 記事を追加する

`content/posts/` に Markdown ファイルを1つ作るだけです。ファイル名は `YYYY-MM-DD-タイトル.md` にします。

```markdown
---
title: マシューからのメッセージ　２０２６年１０月１日
date: 2026-10-01T09:00:00Z
categories:
  - マシューからのメッセージ
cover:
  image: /uploads/2026/10/写真.jpg   # 省略可。画像は static/uploads/ に置く
---

ここに本文を書きます。空行で段落を分けます。

YouTube を埋め込むときは、そのURLだけの行を書きます:
{{< youtube 動画ID >}}

対話形式の記事で、もう一方の話し手（例: ブロッサム）の段落を別の色にするには、
その段落のすぐ下の行に {.alt} と書きます:

ブロッサム：おはようございます。
{.alt}

光連合：おはようございます、ブロッサム。
```

- 本文は自動で紺色の太字、`{.alt}` の段落は紫色になります（旧サイトと同じ配色）。色の指定を本文に書く必要はありません。

- `date` を未来にすると公開されません（下書き扱い）。`draft: true` でも同じです。
- 記事のURLはタイトルから自動で決まります。旧サイトから移行した記事は `url:` で元のURLを保っているので変更しないでください。
- カテゴリー名は既存のものと完全に同じ表記にすると同じカテゴリーにまとまります。既存: エレーナ・ベラスケス経由のメッセージ / エリック・クライン　クリスタルの階段 / ナタリー・グラッソン経由のメッセージ / ブロッサム・グッドチャイルド / マイク・クインシー / マシューからのメッセージ / マシューからのメッセージ　（過去のメッセージ） / 光のメッセージ / 天使

## 記事を直す・消す

該当する `content/posts/*.md` を編集・削除して push するだけです。

## 構成

| 場所 | 内容 |
|---|---|
| `content/posts/` | 記事（Markdown、1記事1ファイル） |
| `static/uploads/` | 画像。`/uploads/...` のURLで参照 |
| `hugo.yaml` | サイト設定（タイトル、メニュー、URL） |
| `themes/PaperMod/` | テーマ（そのままコピーして同梱。変更しない） |
| `layouts/` | テーマの上書き（マゼンタのヘッダー、右サイドバー、カテゴリー表示） |
| `assets/css/extended/custom.css` | 配色・フォント・レイアウト（旧サイトの見た目を再現） |
| `.github/workflows/hugo.yml` | 自動ビルド・公開 |

## ローカルで確認する

```bash
brew install hugo
hugo server
# http://localhost:1313/ を開く
```

## ドメインの切り替え（後で）

詳しい手順と SEO チェックリストは [LAUNCH.md](LAUNCH.md) を参照。

1. リポジトリの Settings → Pages → Custom domain に `www.despertando-nihongo.com` を設定
2. Infomaniak の DNS で `www` を CNAME `despertando-nihongo.github.io` に、`@` を GitHub Pages の A レコードに向ける
3. `hugo.yaml` の `baseURL` を `https://www.despertando-nihongo.com/` に変更
