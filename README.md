# こたつLLM速報 (KOTATSU LLM TIMES)

LLM・SLM・音声LLM・大規模深層学習モデルの動向を毎日**こたつから**速報する、
**ネタ系・非営利**の個人運営ニュースサイトのソースリポジトリです。

> ⚠ 本サイトの記事はすべて LLM（大規模言語モデル）が自動生成しており、
> 人間による校閲を経ていません。内容の正確性は保証されません。
> 詳細は [免責事項](docs/disclaimer.md) を参照してください。

## サイト構成

- 公開先: GitHub Pages（`docs/` フォルダから配信）
- 静的サイトジェネレータ: Jekyll（GitHub Pages ネイティブ）
- 記事URL: `/news/YYYY/MM/DD/`

```
LLM_Daily_News/
├── docs/                       # GitHub Pages 配信元（Jekyll サイト）
│   ├── _config.yml             # Jekyll 設定
│   ├── _layouts/               # ページ・記事のレイアウト
│   ├── _includes/              # マストヘッド・ナビ・フッター
│   ├── _posts/                 # 日次記事（YYYY-MM-DD-daily.md）
│   ├── assets/css/main.css     # 新聞風スタイル
│   ├── index.html              # トップ（最新号一覧）
│   ├── archive.html            # アーカイブ（年・月別）
│   ├── about.md                # 本紙について
│   ├── disclaimer.md           # 免責事項
│   └── Gemfile                 # ローカルプレビュー用依存
├── data/
│   └── seen_urls.json          # 記事化済みURL台帳（重複検知用）
├── CLAUDE.md                   # 日次記事生成の作業手順
└── README.md
```

## GitHub Pages の有効化

リポジトリの **Settings → Pages** にて以下を設定してください。

- **Source**: Deploy from a branch
- **Branch**: `main` / **Folder**: `/docs`

数分後に `https://<owner>.github.io/<repo>/` で公開されます。

> **メモ**: ユーザー/オーガニゼーションPagesではなくプロジェクトPagesとして公開する場合、
> `docs/_config.yml` の `baseurl` をリポジトリ名（例: `/LLM_Daily_News`）に設定してください。

## ローカルプレビュー

```bash
cd docs
bundle install            # 初回のみ
bundle exec jekyll serve  # http://localhost:4000
```

## 新しい記事の追加

[`CLAUDE.md`](CLAUDE.md) の「毎日の作業フロー」を参照してください。
記事は `docs/_posts/YYYY-MM-DD-daily.md` 形式で追加し、Jekyll の front matter を必ず付与します。

## ライセンス

本リポジトリのコンテンツには **[Kotatsu LLM License (KLL-1.0)](LICENSE.md)** が適用されます。

本コンテンツはすべて大規模言語モデルによって自動生成されており、人間による著作的寄与は存在しません。
著作者は存在せず、利用・複製・改変・頒布に制限はありません。詳細は [LICENSE.md](LICENSE.md) を参照してください。

- 記事中の企業名・製品名は各社の商標・登録商標です。
- 元記事へのリンクは引用目的です。
- 第三者権利の侵害に関する申し立ては Issue にてご連絡ください。
