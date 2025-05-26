---
title: "【2025/5】Cursorの機能色々増えているようなので、改めて公式ドキュメントを読んでまとめる"
emoji: "🚃"
type: "tech"
topics: ["Cursor", "AI"]
published: true
---
# モチベーション
Cursorはなんとなく使ってきたけど、Background Agents (Preview)がリリースされて、改めて使いこなせていない機能がたくさんあることに気づいたので、公式ドキュメントを読んで情報整理する。


# 編集機能
## Tab 
### 自動コード補完
コンテキストを認識して続くコードを自動的に保管してくれる。
Github Copilotと同じ機能だけど、copilotより優秀らしい。

### 自動インポート
TypScriptとPythonプロジェクトでは、Tabキーでモジュールを自動インポートしてくれる。

## チャット
### Agent Mode
AIがプロジェクトのコンテキストを理解した上で、プロジェクト内のfileをあれこれを実装してくれる。

### Ask mode
AIがプロジェクトのコンテキストを理解した上で、質問に回答してくれる。

### Manual Mode
ユーザーがファイルを明示的にAIに伝えて、編集するモード。
必要な変更内容とファイルをユーザーが理解している場合は、Manual Modeが良いかもとのこと。

#### 使用例
特定の関数名のrename
> "In @src/utils/helpers.ts and @src/components/UserProfile.tsx,rename the function getUserData to fetchUserProfile and update all call sites within these files."

### Custom Modes
特定のワークフロー向けにAIアシスタンスをカスタマイズするできる。  
「MPCのこの機能使用してほしいのに～」ってときに使えそう。

![](https://storage.googleapis.com/zenn-user-upload/b9e49cb71d08-20250526.png)

## インライン編集
### インライン編集（Cmd/Ctrl+K）
Cmd/Ctrl+KでコードのインラインでチャットのTextFieldが出現して、コードを修正できる。
![](https://mintlify.s3.us-west-1.amazonaws.com/cursor/images/cmdk/regular.png)

## ターミナルコマンドK
ターミナルで使用できるコマンド生成に特化したチャット。
![](https://mintlify.s3.us-west-1.amazonaws.com/cursor/images/cmdk/terminal-cmdk.png)


## AI Commit Message
コンテキストに応じた Git コミットメッセージを自動的で生成してくれる

gitタブのキラキラボタンを押すだけ。
![](https://mintlify.s3.us-west-1.amazonaws.com/cursor/images/features/generate-commit-message.png)

## Notepads (Beta)
開発作業の中で「コンテキスト（文脈）」を柔軟に共有・再利用できる、強力な情報共有ツールです。従来の .cursorrules よりも拡張性が高い。
### 特徴
- チャットやエディタのComposer間で、ノートパッドに書いた内容を簡単に参照・共有できる
- ドキュメントやリファレンスファイルをノートパッドに添付できる
- ノートパッドや添付ファイルは @ 記法で呼び出せます。たとえば @api-specs.yaml のように記述して、他の場所から参照できる。
- Markdown形式で自由に情報を整理でき、見出しや例、ルール、ガイドラインなどを分かりやすくまとめられる。

複数プロジェクトにわたって再利用できるルールなどを定義すると良さそう。
Note専用のrepositoryを作って、gitでversion管理する形になるかな。

(2025/05/26)
現状、私の環境ではNotepads機能は使えないみたい。


## Background Agents (Preview)
バックエンドでAgentがよりロングランなタスクを実行してくれる。
GitHubに接続して、repositoryをpull、branchを切って実装を行ってくれる。
PR作成までやってくれるとは**記載されていない。**

> バックグラウンド エージェントは現在プレビュー段階であり、限られた数のユーザーに展開されています。
> バックグラウンド エージェントを使用するには、プライバシー モードをオフにする必要があります。

(2025/05/26)
現状、私の環境ではNotepads機能は使えないみたい。

# Context
## Codebase Indexing
コードベースをインデックス化することで、AIがより正確な実装を行えるようにする機能。
プロジェクトを開くとCursorが勝手にインデックスをさくせいしてくれるので、無意識でOK。

## Rules
AIを制御するためのプロジェクトのルールを設定できる。

### 設定ファイルの場所
path: .cursor/rules/*.mdc

### 設定のType
| ルールタイプ | 説明 |
|------------|------|
| Always | モデルのコンテキストに常に含まれる |
| Auto Attached | グロブパターンに一致するファイルが参照された時に含まれる |
| Agent Requested | AIが利用可能で、含めるかどうかをAIが判断する。説明が必要 |
| Manual | @ruleName を使用して明示的に言及された場合のみ含まれる |

### ベストプラクティス
- ルールは簡潔に。500行以下を目安にする
- 大きな概念は複数の、組み合わせ可能なルールに分割する
- 具体的な例や参照ファイルを提供する（役立つ場合）
- 曖昧なガイダンスを避ける。社内ドキュメントを書くように明確なルールを書く
- チャットで同じプロンプトを繰り返し使う場合は、ルールを再利用する


.cursorrules は、すでにLegacyになっていて、廃止予定。

## Managing Context
![](https://mintlify.s3.us-west-1.amazonaws.com/cursor/images/context/context-management/menu-open.png)
チャットのときに、特定のfileやfolder、モジュールのドキュメント、過去のチャットをコンテクストとして明示的にAIに渡すことができる。
サイズが大きすぎてコンテキストに入り切らない場合は、Cursorが勝手に圧縮してくれる。

### @ symbols
- `@Files` - プロジェクト内の特定のファイルを参照
- `@Folders` - より広いコンテキストのためにフォルダ全体を参照
- `@Code` - コードベースから特定のコードスニペットやシンボルを参照
- `@Docs` - ドキュメントやガイドにアクセス
- `@Git` - git履歴や変更にアクセス
- `@Notepads` - ノートパッドにアクセス
- `@Past Chats` - 要約されたcomposerセッションで作業
- `@Cursor Rules` - cursorルールで作業
- `@Web` - 外部のWebリソースやドキュメントを参照
- `@Link (paste)` - 特定のコードやドキュメントへのリンクを作成
- `@Recent Changes` - 特定のコードやドキュメントへのリンクを作成
- `@Lint Errors` - リンターエラーを参照 (Chatのみ)
- `@Definitions` - シンボル定義を検索 (Cmd Kのみ)

## Ignore Files
`.cursorignore`をroot dirに作成して、Cursor がアクセスできるディレクトリとファイルを制御できる。


### ユースケース
- セキュリティー的にcursorに見られたくないfileなを登録
- パフォーマンス向上のために、開発には無関係なファイルを登録

## Model Context Protocol (MCP)
外部ツールと連携する方法。
Cursorの設定のMCPにJsonを設定することで使用できる。
Composer Agentが、関係ありそうだと思ったときは自動的に登録されているツールを活用してくれる。

## Max Mode
通常モードより大きなContextを持っているモード。

> Max Modeは、モデルに最も深い思考と推論が必要な、最も難しい問題に特に効果的です。ほとんどのタスクでは通常モードの使用をお勧めします。