# jazz-website — 個人サイト（ジャズピアニストとしての活動）

ライブ情報・アルバム・プロフィールを載せた公開サイト。**院内の仕事とは無関係の、対外的な個人サイト。**

- 公開: https://jazz-website-six.vercel.app/
- GitHub: `tslab1978/jazz-website`（Vercel と連携し、push で自動デプロイ）

## 本体はどこか

| 対象 | 場所 |
|---|---|
| サイト本体 | `index.html` の1ファイル。**これが全部**（約27KB） |
| 構成・運用手順 | `README.md`（一次情報。スプレッドシートの列定義まで書いてある。作業前に読む） |
| 画像 | `hero_piano_image.jpg`（Hero 背景）、`profile.jpg`、アルバムアート5点 |

ビルドもフレームワークも無い。`package.json` は無い。単一ファイルであることが設計上の選択。

## ライブ情報はコードに書かれていない

サイトの中身は Google スプレッドシートから読んでいる。

```
AppSheet（ライブ・共演者を登録）
  → Google スプレッドシート「TS Lab Manager」
      シート1 ライブ・公演管理 / シート2 FlierURLs（GAS が自動生成）/ シート3 共演者マスタ
  → index.html が読んで表示
  → Vercel（GitHub 連携で自動デプロイ）
```

スプレッドシート ID: `1MMzsBjtkN3wj4a-UzWeMBdWdbMjuDW1bbBS0cCAexHA`

**「ライブ情報が違う」「表示されない」はコードのバグとは限らない。** まずスプレッドシート側の
行・列（日時は `2026/01/02` 形式、共演者 URL は VLOOKUP で自動入力）を疑う。
列の対応は `README.md` の表にある。列を足す・入れ替えるときは `index.html` の読み取り側と必ず両方直す。

## 触らないこと

- `main` への push は本番公開と同義。**必ず確認を取ってから行う**
- `index_backup2.html` は旧版の控え。編集対象ではないが、消す判断も勝手にしない
- 画像は差し替え前に必ず元を確認する（`profile.jpg` は約690KB、`hero_piano_image.jpg` は約550KB）。
  Hero 画像の `opacity` は 0.55 → 1 に変えた経緯がある。戻す提案をするなら理由を添える

## 書きぶり

対外的な個人サイトなので、**院内の固有名詞（病院名・部署名・肩書き）を載せない。**
音楽活動の文脈だけで書く。
