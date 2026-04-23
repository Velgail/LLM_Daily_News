# こたつLLM速報 (LLM Daily News) — Claude 作業手順

## リポジトリ構造

```
LLM_Daily_News/
├── docs/                              ← GitHub Pages 配信元（Jekyll サイト）
│   ├── _config.yml
│   ├── _layouts/  _includes/  assets/
│   ├── _posts/
│   │   └── YYYY-MM-DD-daily.md        ← 日次ニュース記事（Jekyll 投稿）
│   ├── index.html / archive.html
│   ├── about.md / disclaimer.md
│   └── Gemfile
├── data/
│   └── seen_urls.json                 ← 記事化済みURL台帳（重複検知）
├── README.md
└── CLAUDE.md
```

公開先: GitHub Pages（Settings → Pages → Source: `main` branch / `/docs` folder）。
サイト名は **「こたつLLM速報」**。記事は LLM 自動生成・校閲なし・非営利。

---

## 対象スコープ

以下のトピックを対象とする。

| カテゴリ | 内容 |
|---|---|
| LLM | 大規模言語モデル全般。大手（OpenAI / Google / Anthropic / Meta / Mistral 等）のモデル更新は**取りこぼし厳禁** |
| SLM | 小規模言語モデル（Phi / Gemma / Llama 系の軽量版等） |
| 音声LLM | 音声入出力を持つ大規模モデル（GPT-4o Voice / Gemini Live 等） |
| 大規模Deep Learning | 上記以外の大規模DLモデル（画像生成・動画生成・マルチモーダル等）も含む |

境界ケースは「大規模かつ実用的なDLモデルか」を判断基準にする。

---

## 毎日の作業フロー

**順序が重要**：情報収集は seen_urls.json を参照する前に行う。
先に既出URLを見ると選定に認識誘導が生じるため、収集→重複排除の順を厳守する。

### 1. 情報収集（新しい順）

seen_urls.json を**まだ読まずに**、Web検索で**直近24〜48時間**のニュースを収集する。「速報」を名乗る以上、鮮度が最優先。

- **検索クエリに日付フィルタを必ず付ける**（例: `after:YYYY-MM-DD`、`past 24 hours`、`past week` 等）。検索ツールの新しい順ソートを使う
- **各候補について公開日（published date）を必ず確認**し、候補リストに併記する
  - 原則: 実行日から **7日以内** のものだけを採用
  - 例外: 大手の主要モデル発表（OpenAI / Google / Anthropic / Meta / Mistral / xAI 等の新モデル・新バージョン）で、**初出から7日以内かつ seen_urls.json 未登録**であれば採用可
  - 「published: 不明」の記事は不採用（古い再掲の可能性が高い）
- 新しいものから順に候補を列挙する
- 大手モデルの更新情報は必ず含める
- 候補は記事化する本数より多めに集めておく

### 2. 重複チェック

候補URLと**公開日・タイトル・トピックキー**が揃ったら、初めて `data/seen_urls.json` を読み込む。
URL完全一致だけでは「同じニュースが別URLで再掲される」ケースを取りこぼすため、以下の3段階で重複排除を行う。

#### 2-1. URL正規化してから一致チェック

比較前に各URLを以下の規則で正規化する（候補側・台帳側ともに）:

- スキームを `https` に統一、ホストは小文字化、`www.` プレフィックス除去
- トラッキング系クエリパラメータを除去: `utm_*`, `fbclid`, `gclid`, `mc_cid`, `mc_eid`, `ref`, `ref_src`, `ref_url`, `s`, `t`, `igshid`, `_hsenc`, `_hsmi`, `share`, `share_id`
- 末尾スラッシュ `/` を除去、フラグメント `#...` を除去
- AMPバリアント（`/amp/`, 末尾 `?amp=1` など）を非AMP版に正規化

正規化後に台帳の `urls` キー（同じく正規化）と完全一致するものは除外。

#### 2-2. トピックキーによる意味的重複チェック

各候補に **`topic_key`**（小文字英数+ハイフンのスラッグ）を付与する。例:

- Anthropic Claude Opus 4.7 のリリース → `claude-opus-4.7-release`
- Google Gemini 3.1 Flash TTS の発表 → `gemini-3.1-flash-tts-release`
- OpenAI Sora 2 の API 公開 → `sora-2-api-launch`

台帳の各エントリ（`topic_key` フィールド）と一致するものは、URLが違っても**重複とみなして除外**する。

#### 2-3. 直近の投稿との突き合わせ

`docs/_posts/` の**直近14日分**のファイルを読み、見出し（`### N. ...`）と本文中のリンクを確認。同一トピックを既に紹介している場合は除外する（台帳追記漏れを救済するための保険）。

#### 2-4. 報告と確定

除外したURL・トピックを「除外理由（URL一致 / topic_key一致 / 過去投稿一致）」付きでユーザーに報告し、残りを記事化対象として確定する。

### 3. 記事ファイルの作成

出力先パス: `docs/_posts/YYYY-MM-DD-daily.md`

- `docs/_posts/` ディレクトリは既存。年・月のサブディレクトリは不要（Jekyll の `_posts` 規約に従う）。
- 同日ファイルが既に存在する場合は末尾に追記（`---` で区切る）。
- 公開URLは自動的に `/news/YYYY/MM/DD/` になる（`docs/_config.yml` の `permalink` 設定）。

### 4. 記事フォーマット

ファイル冒頭に **Jekyll front matter** を必ず入れる。本文の `# H1` は不要（レイアウトが提供）。

```markdown
---
title: "YYYY-MM-DD のLLM/AI業界ニュース"
date: YYYY-MM-DD
---

## 📰 本号のニュース

### 1. [キャッチーな日本語タイトル]

> [1文のリード：技術的驚きかビジネスインパクトを凝縮]

[読者が「続きを読みたい」と感じる紹介文 3〜5文。
 ・この記事で何が明らかになるか
 ・なぜ今この話題が重要か
 ・開発者・ビジネス担当者に何が変わるか]

🔗 **[元記事を読む →](URL)**

---

### 2. ...（以下同様）

---

## 🔍 独自考察：今日のトレンド深掘り

### 本号のニュースが示すもの
### 技術的インプリケーション
### ビジネス・産業への影響
### 見落とされがちな重要ポイント
### 今後3〜6ヶ月の展望
### 読者への具体的アクション提案（3点）

---
*Generated: YYYY-MM-DD*
```

#### 品質基準
- **前半（ニュース紹介）**：読者がURLをクリックしたくなる誘導記事。内容を要約するのではなく「何が面白いか」「何を得られるか」を伝える。
- **後半（独自考察）**：1500字以上。有償販売可能な品質を目標とし、表面的な報道にない洞察・予測・アクション提案を含める。

### 5. seen_urls.json の更新

記事作成後、新規URLを `data/seen_urls.json` の `urls` オブジェクトに追記する。
重複検知強化のため、**正規化済みURL**をキーに、値はオブジェクト形式で `date`（初出日）・`title`（記事タイトル）・`topic_key`（トピックスラッグ）を記録する。

```json
{
  "_comment": "記事化済みURL台帳。key=正規化済みURL（utm等除去・末尾スラッシュ除去・小文字host）。value はオブジェクト {date, title, topic_key} を推奨。古いエントリは値が日付文字列のみの後方互換形式。重複検知に使用。",
  "urls": {
    "https://example.com/article": {
      "date": "2026-04-23",
      "title": "Example Model X.Y Released",
      "topic_key": "example-model-x.y-release"
    },
    "https://legacy.example.com/old": "2026-04-19"
  }
}
```

注意:

- **既存の文字列形式エントリ（`"URL": "YYYY-MM-DD"`）は変換せずそのまま残す**（後方互換）
- 新規追記は必ずオブジェクト形式で `topic_key` を含める
- 同じ `topic_key` を持つエントリが既にある場合は記事化していないはずなので、追記前にもう一度確認

### 6. コミット＆プッシュ

```
git add docs/_posts/YYYY-MM-DD-daily.md data/seen_urls.json
git commit -m "news: YYYY-MM-DD (N記事)"
git push -u origin <branch>
```

---

## 注意事項

- URLは **必ず正確に** 記事内リンクに埋め込む（誤リンク禁止）。台帳に保存するのは正規化後URLだが、**記事本文のリンクは公開ページへ正しく遷移する元URL**を使う（一部サイトはトラッキング除去で404になるため）。
- 独自考察は一般論に終始せず、今日のニュース群を根拠にした具体的分析にする。
- 重複チェックは収集完了後に行う。収集前に seen_urls.json を参照しない。
- **鮮度厳守**: 公開日が確認できない記事、および7日より古い記事は原則採用しない。「速報」を看板に掲げている以上、過去の記事の再紹介は信頼性を損なう。
- **トピック重複厳禁**: 同じモデル発表・同じイベントを別URL・別ソースで再掲載しない。`topic_key` が一致したら既出として扱う。
