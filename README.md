# tou AI をウェブに公開する手順

## 構成
```
tou-ai/
├── api/
│   └── chat.js      ← APIキーを扱うサーバー側の処理（Vercel Serverless Function）
├── public/
│   └── index.html   ← 画面（ブラウザ側）
└── vercel.json
```

`public/index.html` はもう外部にAPIキーを送っていません。代わりに自分のサーバー
(`/api/chat`) にメッセージを送り、サーバーが安全にAnthropicへ問い合わせます。

---

## 手順（Vercelを使う方法・無料）

### 1. Anthropic APIキーを取得
1. https://console.anthropic.com にアクセスしてログイン（アカウントがなければ作成）
2. 左メニューの「API Keys」から新しいキーを発行
3. `sk-ant-...` から始まる文字列をコピーしておく（他人に見せないこと）

※ APIの利用には別途料金がかかります（claude.aiのサブスクとは別）。使った分だけの従量課金です。

### 2. GitHubにアップロード
1. https://github.com で新しいリポジトリを作成（例: `tou-ai`）
2. この `tou-ai` フォルダの中身一式をそのリポジトリにアップロード
   （GitHubのウェブ画面から "Add file" → "Upload files" でドラッグ&ドロップでもOK）

### 3. Vercelでデプロイ
1. https://vercel.com にアクセスし、GitHubアカウントでログイン
2. 「Add New」→「Project」→ 先ほどのリポジトリを選択
3. 「Environment Variables」の欄で以下を追加
   - Key: `ANTHROPIC_API_KEY`
   - Value: 手順1でコピーしたAPIキー
4. 「Deploy」をクリック

数十秒〜数分でデプロイが完了し、`https://あなたのプロジェクト名.vercel.app` のようなURLが発行されます。
これが公開URLです。誰でもこのURLにアクセスして会話できます。

---

## 注意点

- **ログイン画面はダミー**: 現状のコードのログイン画面は名前とメールの形式チェックをしているだけで、
  実際の認証やデータ保存は行っていません。公開して不特定多数に使わせる場合、想定外のAPI利用料が
  発生する可能性があります。身内での利用に留めるか、簡易パスワード等の制限を検討してください。
- **会話履歴は保存されない**: ページを閉じたり再読み込みすると会話はリセットされます（ブラウザに
  保存する仕組みは入れていません）。
- **利用料金**: `/api/chat` は呼ばれるたびにAnthropic APIを課金消費します。公開URLを広く共有する
  場合は、Anthropicコンソールで利用上限（Spending limit）を設定しておくことをおすすめします。

## ローカルで動作確認したい場合
Vercel CLIを使うと自分のPCでも動作確認できます。
```
npm install -g vercel
cd tou-ai
vercel dev
```
