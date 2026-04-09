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
AppSheet（ライブ登録）
    ↓
Googleスプレッドシート（TS Lab Manager）
    ↓ シート1: ライブ・公演管理
    ↓ シート2: FlierURLs（GASが自動生成）
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
| M | フライヤーイメージ（AppSheetが自動保存） |
| N | 詳細テキスト（メンバー・説明・URL等） |

### シート2: FlierURLs
GASが自動生成。A列=日時、B列=Google Drive画像URL。  
**手動更新はしない。GASが毎朝6時に自動更新。**

---

## Google Apps Script（GAS）

**場所:** スプレッドシート → 拡張機能 → Apps Script

### addImageURLs()
FlierURLsシートを更新する関数。  
- ライブ・公演管理のM列のファイル名からGoogle DriveのファイルIDを検索
- FlierURLsシートにA列=日時、B列=URLを書き込む

### setTrigger()
毎朝6時にaddImageURLs()を自動実行するトリガーを設定。  
**※ 一度だけ実行すれば設定完了。再実行不要。**

### 新しいライブを追加したとき
1. AppSheetでライブ情報とフライヤー画像を登録
2. 翌朝6時にGASが自動でFlierURLsを更新
3. サイトに画像が表示される

---

## Google Driveフライヤー画像フォルダ

**フォルダID:** `1Rk3MuVrQ2zW_7JnstKZ-JXxtpEKY0kxn`  
**共有設定:** リンクを知っている全員が閲覧可能  
AppSheetが自動で画像を保存するフォルダ。

---

## サイトの更新方法

### ライブスケジュールの更新
AppSheetで登録するだけ。サイトは自動反映。

### プロフィール文・写真の更新
1. `index.html` または `profile.jpg` を修正
2. GitHubのリポジトリページでファイルを編集またはアップロード
3. Vercelが自動でデプロイ（数分で反映）

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
4. 「Commit changes」をクリック
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

## トラブルシューティング

### ライブスケジュールが表示されない
→ スプレッドシートの共有設定を確認（リンクを知っている全員が閲覧可能）

### フライヤー画像が表示されない
→ Google Driveフォルダの共有設定を確認  
→ GASを手動実行してFlierURLsを更新

### AppSheetのエラー「column mismatch」
→ スプレッドシートの列を追加・削除した場合に発生  
→ AppSheet → Data → Regenerate Structure で解決

### サイトが更新されない
→ ブラウザで Command + Shift + R（強制リロード）
