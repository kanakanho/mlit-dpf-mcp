# VSCode での MCP サーバーのセットアップ

このドキュメントでは、VSCode で MLIT DATA PLATFORM MCP サーバーを使用する方法を説明します。

## 目次
- [前提条件](#前提条件)
- [対応する VSCode 拡張機能](#対応する-vscode-拡張機能)
- [セットアップ手順](#セットアップ手順)
  - [1. APIキーの取得](#1-apiキーの取得)
  - [2. リポジトリのクローン](#2-リポジトリのクローン)
  - [3. 仮想環境のセットアップ](#3-仮想環境のセットアップ)
  - [4. 環境変数の設定](#4-環境変数の設定)
  - [5. VSCode 拡張機能の設定](#5-vscode-拡張機能の設定)
- [設定ファイルの詳細](#設定ファイルの詳細)
- [トラブルシューティング](#トラブルシューティング)

## 前提条件

- **VSCode**: Visual Studio Code がインストールされていること
- **Python**: Python 3.10 以上がインストールされていること
- **MCP対応拡張機能**: Cline、Continue、またはその他の MCP 対応 VSCode 拡張機能
- **APIキー**: 国土交通データプラットフォームの API キー

## 対応する VSCode 拡張機能

このMCPサーバーは、以下のVSCode拡張機能で使用できます：

### 1. Cline (旧 Claude Dev)
- **拡張機能ID**: `saoudrizwan.claude-dev`
- **Marketplace**: [Cline](https://marketplace.visualstudio.com/items?itemName=saoudrizwan.claude-dev)
- **特徴**: Claude API を使用した AI アシスタント、MCP サーバーの統合サポート

### 2. Continue
- **拡張機能ID**: `continue.continue`
- **Marketplace**: [Continue](https://marketplace.visualstudio.com/items?itemName=Continue.continue)
- **特徴**: 複数の LLM プロバイダーに対応、MCP プロトコルサポート

## セットアップ手順

### 1. APIキーの取得

国土交通データプラットフォームでアカウントを作成し、APIキーを取得します。

詳しい手順は [こちら](https://www.mlit-data.jp/api_docs/usage/introduction.html) をご覧ください。

### 2. リポジトリのクローン

```bash
git clone https://github.com/MLIT-DATA-PLATFORM/mlit-dpf-mcp.git
cd mlit-dpf-mcp
```

### 3. 仮想環境のセットアップ

**Windows:**
```bash
python -m venv .venv
.venv\Scripts\activate
pip install -e .
pip install aiohttp pydantic tenacity python-json-logger mcp python-dotenv
```

**macOS/Linux:**
```bash
python -m venv .venv
source .venv/bin/activate
pip install -e .
pip install aiohttp pydantic tenacity python-json-logger mcp python-dotenv
```

### 4. 環境変数の設定

`.env.example` をコピーして `.env` ファイルを作成します：

```bash
cp .env.example .env
```

`.env` ファイルを編集して、APIキーを設定します：

```
MLIT_API_KEY=your_api_key_here
MLIT_BASE_URL=https://www.mlit-data.jp/api/v1/
```

> **重要**: `your_api_key_here` を実際の API キーに置き換えてください。

### 5. VSCode 拡張機能の設定

#### Cline を使用する場合

1. VSCode で Cline 拡張機能をインストール
2. コマンドパレット（`Ctrl+Shift+P` または `Cmd+Shift+P`）を開く
3. `Cline: Open Settings` を選択
4. 設定画面で "MCP Servers" セクションを探す
5. 以下の設定を追加：

```json
{
  "mcpServers": {
    "mlit-dpf-mcp": {
      "command": "python",
      "args": ["-m", "src.server"],
      "env": {
        "MLIT_API_KEY": "your_api_key_here",
        "MLIT_BASE_URL": "https://www.mlit-data.jp/api/v1/",
        "PYTHONUNBUFFERED": "1",
        "LOG_LEVEL": "WARNING"
      }
    }
  }
}
```

または、プロジェクトルートの `.vscode/mcp-settings.json` を使用する場合：

6. VSCode で mlit-dpf-mcp プロジェクトフォルダを開く
7. `.vscode/mcp-settings.json` ファイルを編集
8. `MLIT_API_KEY` の値を実際のAPIキーに置き換え
9. Cline の設定で「プロジェクトの MCP 設定を使用」を有効化

#### Continue を使用する場合

1. VSCode で Continue 拡張機能をインストール
2. `~/.continue/config.json` を開く（存在しない場合は作成）
3. 以下の設定を追加：

```json
{
  "mcpServers": [
    {
      "name": "mlit-dpf-mcp",
      "command": "python",
      "args": ["-m", "src.server"],
      "cwd": "/path/to/mlit-dpf-mcp",
      "env": {
        "MLIT_API_KEY": "your_api_key_here",
        "MLIT_BASE_URL": "https://www.mlit-data.jp/api/v1/",
        "PYTHONUNBUFFERED": "1",
        "LOG_LEVEL": "WARNING"
      }
    }
  ]
}
```

> **注意**: `/path/to/mlit-dpf-mcp` を実際のプロジェクトパスに置き換えてください。

## 設定ファイルの詳細

### `.vscode/mcp-settings.json` の各項目説明

```json
{
  "mcpServers": {
    "mlit-dpf-mcp": {                    // サーバー名（任意の名前を設定可能）
      "command": "python",                // Python インタープリターのコマンド
      "args": ["-m", "src.server"],      // サーバー起動時の引数
      "env": {                            // 環境変数
        "MLIT_API_KEY": "...",           // 国土交通データプラットフォームのAPIキー（必須）
        "MLIT_BASE_URL": "...",          // APIのベースURL（デフォルト値推奨）
        "PYTHONUNBUFFERED": "1",         // Python の出力バッファリングを無効化（推奨）
        "LOG_LEVEL": "WARNING"           // ログレベル（DEBUG, INFO, WARNING, ERROR から選択）
      }
    }
  }
}
```

### 環境変数の詳細

| 変数名 | 必須 | 説明 | デフォルト値 |
|--------|------|------|--------------|
| `MLIT_API_KEY` | ✅ | 国土交通データプラットフォームのAPIキー | なし |
| `MLIT_BASE_URL` | ⚠️ | APIのエンドポイントURL | `https://www.mlit-data.jp/api/v1/` |
| `PYTHONUNBUFFERED` | 推奨 | Python出力のバッファリング制御 | `1` (無効化) |
| `LOG_LEVEL` | オプション | ログ出力レベル | `WARNING` |

### Python コマンドのカスタマイズ

仮想環境を使用する場合、`command` を仮想環境の Python パスに変更できます：

**Windows の例:**
```json
{
  "command": "C:\\Users\\YourName\\mlit-dpf-mcp\\.venv\\Scripts\\python.exe"
}
```

**macOS/Linux の例:**
```json
{
  "command": "/Users/YourName/mlit-dpf-mcp/.venv/bin/python"
}
```

## トラブルシューティング

### 問題: MCP サーバーが起動しない

**解決方法:**
1. Python がインストールされているか確認
   ```bash
   python --version
   ```
2. 必要なパッケージがインストールされているか確認
   ```bash
   pip list | grep mcp
   ```
3. 環境変数が正しく設定されているか確認
   ```bash
   python -m src.server
   ```

### 問題: API キーエラー

**解決方法:**
1. `.env` ファイルに正しい API キーが設定されているか確認
2. VSCode の設定ファイル内の `MLIT_API_KEY` が正しいか確認
3. API キーの有効期限が切れていないか確認

### 問題: モジュールが見つからないエラー

**解決方法:**
1. 仮想環境が有効化されているか確認
2. 依存関係を再インストール
   ```bash
   pip install -e .
   pip install aiohttp pydantic tenacity python-json-logger mcp python-dotenv
   ```

### 問題: ログが表示されない

**解決方法:**
1. `LOG_LEVEL` を `DEBUG` に変更してより詳細なログを確認
2. `PYTHONUNBUFFERED` が `1` に設定されているか確認

### 問題: VSCode 拡張機能が MCP サーバーを認識しない

**解決方法:**
1. VSCode を再起動
2. 拡張機能を無効化→有効化
3. 設定ファイルの JSON 構文が正しいか確認（[JSONLint](https://jsonlint.com/) で検証）
4. 拡張機能のドキュメントで最新の設定方法を確認

## 参考リンク

- [国土交通データプラットフォーム API ドキュメント](https://www.mlit-data.jp/api_docs/usage/introduction.html)
- [Model Context Protocol (MCP) 仕様](https://modelcontextprotocol.io/)
- [Cline 拡張機能](https://marketplace.visualstudio.com/items?itemName=saoudrizwan.claude-dev)
- [Continue 拡張機能](https://marketplace.visualstudio.com/items?itemName=Continue.continue)

## サポート

問題が解決しない場合は、以下のリソースをご利用ください：

- [GitHub Issues](https://github.com/MLIT-DATA-PLATFORM/mlit-dpf-mcp/issues)
- [国土交通データプラットフォームお問い合わせフォーム](https://docs.google.com/forms/d/e/1FAIpQLScHlMUInwpoyREX672SFJuwo8ZfpllQUatPuYNRiKYZkoe6nQ/viewform)
