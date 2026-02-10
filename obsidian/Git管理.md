Obsidian VaultをGit管理する定番構成を、**事故りにくい運用**（実用性・安全性優先）でまとめます。

（前提：Vault＝普通のフォルダ、Markdown中心）

---

## **0) まず方針（おすすめ）**

- **個人用**：GitHub / GitLab の **Private repo**
    
- **目的**：履歴・差分・復元・複数端末同期（+衝突回避）
    

---

## **1) VaultをGitリポジトリにする（最短）**

  

### **A. リポジトリ作成**

  

GitHub/GitLabで空のprivate repoを作る（READMEなしでもOK）

  

### **B. ローカルで初期化してpush**

  

ターミナルでVaultフォルダに移動して：

```
cd "/path/to/YourVault"
git init
git branch -M main
git add .
git commit -m "init vault"
git remote add origin git@github.com:YOURNAME/YOURVAULT.git
git push -u origin main
```

※HTTPSでも可（毎回認証が面倒ならSSH推奨）
git init
git branch -M main
git add .
git commit -m "init vault"
git remote add origin https://github.com/Tomoaki-Hamanaka/obsidian.git
git push -u origin main

---

## **2) 競合を減らす .gitignore（重要）**

  

Vault直下に .gitignore を作って、まずこれを入れるのが安全です。

```
# OS
.DS_Store
Thumbs.db

# Obsidian cache / workspace (競合しやすい)
.obsidian/workspace.json
.obsidian/workspace-mobile.json
.obsidian/cache/
.obsidian/graph.json

# プラグインが生成するもの（必要に応じて）
.obsidian/plugins/*/data.json
.obsidian/plugins/*/cache/

# ごみ箱系（使っていれば）
.trash/
```

**ポイント**

- workspace*.json は端末ごとに差が出やすく、衝突の温床なので基本除外。
    
- .obsidian/ を全部ignoreすると設定共有ができないので、最初は「衝突しやすいものだけ」外すのが無難。
    

  

.gitignore を入れたら：

```
git add .gitignore
git commit -m "add gitignore for obsidian"
git push
```

---

## **3) Obsidian Git プラグインで自動化（推奨）**

  

デスクトップならほぼこれで運用が楽になります。

  

### **セットアップ**

1. Obsidian → **Settings → Community plugins**
    
2. **Safe mode OFF**
    
3. Browse → **“Obsidian Git”** を検索してインストール＆有効化
    
4. Settings → **Obsidian Git** を開く
    

  

### **おすすめ設定**

- **Auto pull on startup**：ON（起動時にpull）
    
- **Auto commit interval**：5〜30分（お好み）
    
- **Auto push**：ON（commit後にpush）
    
- Commit message：vault: {{date}} みたいにテンプレ化
    

---

## **4) 日常運用フロー（衝突しにくい）**

  

### **基本ルール（これだけ守る）**

- **編集前にpull**（Obsidian Gitで起動時pullが最強）
    
- **編集後はこまめにcommit/push**
    
- **同じノートを複数端末で同時に開きっぱなしにしない**
    

  

### **例（理想）**

- Macで作業 → 自動commit/push
    
- iPhoneで読む/追記 → 後述の方法でpull/push
    
- どちらも「作業開始時にpull」が徹底できれば衝突は激減
    

---

## **5) iPhone/iPadでのGit運用（現実解）**

  

iOSはObsidian単体でGitがしづらいので、定番は2つです。

  

### **A案：Working Copy（安定）**

1. Working Copyでrepoをclone
    
2. ObsidianでそのフォルダをVaultとして開く
    
3. 変更したらWorking Copyでcommit/push
    
4. 他端末の変更はpull
    

  

### **B案：iOSはObsidian Sync、PC側はGit（ハイブリッド）**

- **iOSはSyncで快適**
    
- **PCで定期的にGitへスナップショット**
    
    （「思考の履歴」だけGitに残したい場合に強い）
    

---

## **6) コンフリクトが起きたときの対処**

  

### **まず確認**

```
git status
git diff
```

### **よくある原因**

- workspace.json を追跡している（→ignore推奨）
    
- 端末A/Bで同じノートを同時編集
    

  

### **解決の基本**

- 競合したファイルを開き、<<<<<< ====== >>>>>> を手で統合
    
- 統合後：
    

```
git add <file>
git commit -m "resolve conflict"
git push
```

---

## **7) セキュリティ（最低限これ）**

- リポジトリは **Private**
    
- 公開したいなら **公開用Vaultを分ける**
    
- APIキー等がノートにあるなら：
    
    - Secrets.md を作って .gitignore へ
        
    - または暗号化ツール（age等）を使う
        
    

---

## **どの運用にしたい？**

  

すぐに手順をあなた用に最適化できます。次のどれですか？

1. **Macだけ**でGit管理（いちばん簡単）
    
2. **Mac + iPhone**でもGitで同期（Working Copy）
    
3. **iPhoneはSync / PCはGitで履歴**（ハイブリッドで最強）
    

  

あなたの端末構成（Mac/Win/iPhone/Android）だけ教えてくれたら、その前提でコマンドと設定を固定して書きます。