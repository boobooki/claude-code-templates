# Claude Code Templates 詳細解説ガイド

本ドキュメントでは、Anthropicの「Claude Code」を拡張するエコシステムである **Claude Code Templates** の内部構造、高度な機能、およびカスタマイズ方法について詳細に解説します。

---

## 1. アーキテクチャの概要

Claude Code Templates（以下CCT）は、単なる設定ファイルの集まりではなく、Claude Codeの機能を動的に拡張するためのCLIツール（`cct`）を中心としたプラットフォームです。

### 核心となる仕組み
CCTは、Claude Codeが読み込む以下の設定ファイルを自動生成・管理します：
- **`.claude/settings.json`**: フック（Hooks）やタイムアウト設定を定義。
- **`.claude/commands/`**: `/test` や `/refactor` などのカスタムスラッシュコマンドを格納。
- **`CLAUDE.md`**: プロジェクトのコンテキスト、規約、利用可能なツールをClaudeに教えるための指示書。
- **`.mcp.json`**: 外部ツール（GitHub, PostgreSQL等）と連携するためのModel Context Protocol設定。

---

## 2. 主要コンポーネントの詳細

### 🤖 専門エージェント (Agents)
エージェントは、特定の役割（例：フロントエンド開発者、セキュリティ監査員）に特化したシステムプロンプトのセットです。
- **ローカルインストール**: プロジェクトごとに最適なエージェントを配置。
- **グローバルエージェント**: `npx cct --create-agent <name>` を実行すると、`/usr/local/bin` 等に独立した実行ファイルが生成されます。これにより、`frontend-developer "このコードをレビューして"` のように、Claude Codeを介さず直接エージェントをコマンドとして呼び出せます。

### 🪝 フック (Hooks)
Claude Codeの動作の節目に自動実行されるスクリプトです。
- **PreToolUse**: ツール実行前に実行（例：コード変更前にテストを実行）。
- **PostToolUse**: ツール実行後に実行（例：コード生成後にPrettierで自動整形）。
- **Notification**: 特定のイベント発生時に通知（例：ビルド失敗時にDiscordへ通知）。

### ⚡ カスタムコマンド (Commands)
`.claude/commands/` 内のMarkdownファイルとして定義されます。
- **動的スキャン**: CCTのCLIはプロジェクトの言語（JS, Python, Ruby等）を自動検出し、その言語に最適なコマンドセットを提案・インストールします。

---

## 3. 高度な活用ユースケース

### ☁️ E2B サンドボックスによる安全な実行
CCTは [E2B](https://e2b.dev/) と統合されており、以下のコマンドでクラウド上の隔離環境でタスクを実行できます。
```bash
npx claude-code-templates@latest --sandbox e2b --agent security-auditor --prompt "このリポジトリの脆弱性を調査して"
```
- **メリット**: ローカル環境のファイルを破壊するリスクなく、AIに自由な試行錯誤を許可できます。

### 📊 分析とモニタリング
`--analytics` フラグを使用すると、ローカルサーバーが起動し、以下の情報をブラウザで確認できます：
- **トークン消費量**: どのタスクがコストを消費しているか。
- **セッション履歴**: 過去の会話の検索とエクスポート。
- **リアルタイムログ**: Claudeがバックグラウンドで実行しているコマンドの可視化。

---

## 4. カスタマイズと拡張方法

独自のテンプレートやエージェントを作成してチームで共有することが可能です。

### 独自エージェントの作成手順
1. `cli-tool/components/agents/` 内に新しいMarkdownファイルを作成。
2. YAML Frontmatterでメタデータを定義：
   ```markdown
   ---
   name: my-custom-agent
   description: チーム独自の規約に特化したエージェント
   tools: Read, Write, WebSearch
   ---
   あなたは...（システムプロンプト）
   ```
3. `npx cct --agent path/to/your-agent.md` でプロジェクトに適用。

---

## 5. まとめ

Claude Code Templatesは、AIエージェントを「単なるチャット相手」から「自律的な開発パートナー」へと進化させるための強力なツール群です。特に**グローバルエージェント機能**と**サンドボックス統合**は、プロフェッショナルな開発現場において、安全かつ高度な自動化を実現するための鍵となります。

---
*リポジトリ: [boobooki/claude-code-templates](https://github.com/boobooki/claude-code-templates)*
*公式サイト: [aitmpl.com](https://aitmpl.com)*
