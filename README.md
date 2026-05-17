# Takuya Shintani Jazz Website — メンテナンスガイド

## 公開URL
https://jazz-website-six.vercel.app/

## リポジトリ
https://github.com/tslab1978/jazz-website

---

## ファイル構成

```
jazz-website/
├── index.html              # メインHTML（サイト全体）
├── profile.jpg             # プロフィール写真
├── hero_piano_image.jpg    # Heroセクション背景画像（ピアノ鍵盤）
├── Sprout.jpg              # アルバムアート
├── Kamino-Kumano.jpg
├── Little-Acorn.jpg
├── Uranus-Cyan.jpg
├── Encounter-&-Farewell.jpg
└── .gitignore
```

---

## システム構成

```
AppSheet（ライブ登録・共演者登録）
    ↓
Googleスプレッドシート（TS Lab Manager）
    ↓ シート1: ライブ・公演管理
    ↓ シート2: FlierURLs（GASが自動生成）
    ↓ シート3: 共演者マスタ
    ↓
index.html（サイト）が読み込んで表示
    ↓
Vercel（公開）← GitHub連携で自動デプロイ
```

---

## Googleスプレッドシート

**ファイル:** TS Lab Manager  
**ID:** `1MMzsBjtkN3wj4a-UzWeMBdWdbMjuDW1bbBS0cCAexHA`

### シート1: ライブ・公演管理
| 列 | 内容 |
|---|---|
| A | 日時（2026/01/02形式） |
| B | 場所 |
| C | イベント名称 |
| D | 開催地 |
| L | 告知イメージ（AppSheetが自動保存） |
| M | フライヤーイメージ（AppSheetが自動保存） |
| N | 詳細テキスト（メンバー・説明等） |
| O | 共演者1 |
| P | 共演者1 URL（VLOOKUPで自動入力） |
| Q | 共演者2 |
| R | 共演者2 URL（VLOOKUPで自動入力） |
| S | 共演者3 |
| T | 共演者3 URL（VLOOKUPで自動入力） |
| U | 共演者4 |
| V | 共演者4 URL（VLOOKUPで自動入力） |
| W | 共演者5 |
| X | 共演者5 URL（VLOOKUPで自動入力） |
| Y | 共演者6 |
| Z | 共演者6 URL（VLOOKUPで自動入力） |

**共演者URLのVLOOKUP式（P列の例）:**
```
=IF(O2="","",IFERROR(VLOOKUP(O2,共演者マスタ!$A:$B,2,FALSE),""))
```

### シート2: FlierURLs
GASが自動生成。A列=日時、B列=Google Drive画像URL。  
**手動更新はしない。GASが毎朝6時に自動更新。**

### シート3: 共演者マスタ
| 列 | 内容 |
|---|---|
| A | 共演者名（キー列） |
| B | 優先URL（HP・Instagram・FacebookなどメインのURL1つ） |

**新しい共演者を追加するとき:**
1. シート3に名前とURLを追加
2. AppSheetが自動で認識（Regenerate Structure不要）
3. 次回ライブ登録時にドロップダウンで選択可能

---

## AppSheet設定

### 共演者1〜6列の設定
- **Type:** Ref
- **Referenced Table:** 共演者マスタ
- ドロップダウンで共演者マスタから選択できる

### 列追加時の注意
- スプレッドシートの**途中に列を挿入しない**
- 必ず**一番右に追加**する
- 追加後に AppSheet → Data → **Regenerate Structure** を実行

---

## Google Apps Script（GAS）

**場所:** スプレッドシート → 拡張機能 → Apps Script  
**プロジェクト名:** TS Lab GAS（※名前をつけておくこと）

### addImageURLs()
FlierURLsシートを更新する関数。  
- ライブ・公演管理のM列のファイル名からGoogle DriveのファイルIDを検索
- FlierURLsシートにA列=日時、B列=URLを書き込む

### setTrigger()
毎朝6時にaddImageURLs()を自動実行するトリガーを設定。  
**※ 一度だけ実行すれば設定完了。再実行不要。**

### ⚠️ トリガーの自動無効化に注意
Googleのポリシーにより、一定期間操作がないとトリガーが自動削除される。  
**月に一度、FlierURLsシートが更新されているか確認する習慣をつけること。**

トリガーが消えた場合の復旧手順：
1. Apps Script で `addImageURLs()` を手動実行して動作確認
2. `setTrigger()` を実行してトリガーを再設定
3. トリガー一覧（時計アイコン）に「毎朝6時」が表示されることを確認

### 新しいライブを追加したとき
1. AppSheetでライブ情報・フライヤー画像・共演者を登録
2. 翌朝6時にGASが自動でFlierURLsを更新
3. サイトに画像・共演者リンクが表示される

---

## Google Driveフライヤー画像フォルダ

**フォルダID:** `1Rk3MuVrQ2zW_7JnstKZ-JXxtpEKY0kxn`  
**共有設定:** リンクを知っている全員が閲覧可能  
AppSheetが自動で画像を保存するフォルダ。

---

## サイトの更新方法

### ライブスケジュールの更新
AppSheetで登録するだけ。サイトは自動反映。

### 共演者を追加するとき
1. スプレッドシートのシート3（共演者マスタ）に名前とURLを追加
2. AppSheetで該当ライブの共演者1〜6に選択
3. VLOOKUPでURLが自動入力される
4. サイトのポップアップに自動反映

### プロフィール文・写真の更新
1. `index.html` または `profile.jpg` を修正
2. GitHubのリポジトリページでファイルを編集またはアップロード
3. Vercelが自動でデプロイ（数分で反映）

### Hero背景画像の変更・明るさ調整
- ファイル名: `hero_piano_image.jpg`（リポジトリルートに配置）
- 明るさ調整: `index.html` の `.hero-photo-wrap img` の `opacity` を変更（0〜1）
  - 明るくする → opacity を上げる（例: 0.55 → 0.7）
  - 暗くする　 → opacity を下げる（例: 0.55 → 0.4）
- スマートフォンでは非表示（max-width: 640px）

### アルバム情報の更新
1. アルバムアート画像（JPG）を `jazz-website` フォルダに追加
2. `index.html` のDiscographyセクションにHTMLを追加
3. GitHubにアップロード → Vercel自動デプロイ

---

## Vercel自動デプロイ

GitHubのリポジトリを更新するたびに自動でVercelが再デプロイします。

**手順:**
1. https://github.com/tslab1978/jazz-website を開く
2. 更新したいファイルをクリック
3. 鉛筆アイコンで編集 or ファイルをアップロード
4. 「Commit changes」をクリック ← **必ず押すこと！**
5. 数分後にサイトに反映

---

## SNSリンク

| SNS | URL |
|---|---|
| Facebook | https://www.facebook.com/tslab1978 |
| Instagram | https://www.instagram.com/tslabkeys/ |
| YouTube | https://www.youtube.com/@tslab1978 |
| TuneCore | https://www.tunecore.co.jp/artists/tslab1978 |

---

## 今後の実装予定

- [ ] Upcoming Shows デザイン変更（左ボーダーライン・グレーアウト）
- [ ] ポップアップに共演者リンクを表示（O〜Z列から取得）
- [ ] リンクタブ新設（会場・共演者一覧）

---

## トラブルシューティング

### ライブスケジュールが表示されない
→ スプレッドシートの共有設定を確認（リンクを知っている全員が閲覧可能）

### フライヤー画像が表示されない
→ Google Driveフォルダの共有設定を確認  
→ GASを手動実行してFlierURLsを更新  
→ トリガーが消えていないか確認（月1回チェック推奨）

### Hero背景画像が表示されない
→ ブラウザの開発者ツール（Command + Option + I）→ Consoleで404エラーを確認  
→ GitHubに `hero_piano_image.jpg` が正しくアップロードされているか確認  
→ 「Commit changes」を押し忘れていないか確認

### AppSheetのエラー「column mismatch」
→ スプレッドシートの列を追加・削除した場合に発生  
→ AppSheet → Data → Regenerate Structure で解決

### GASトリガーが動いていない
→ Apps Script → 実行数（左メニュー）で過去の実行履歴を確認  
→ トリガー一覧（時計アイコン）が空なら `setTrigger()` を再実行

### サイトが更新されない
→ ブラウザで Command + Shift + R（強制リロード）
