# VSCode MCP 設定

このディレクトリには、VSCode で MLIT DATA PLATFORM MCP サーバーを使用するための設定ファイルが含まれています。

## ファイル

### `mcp-settings.json`

VSCode の MCP 対応拡張機能（Cline、Continue など）用の設定テンプレートです。

**使用方法:**
1. `<YOUR_API_KEY_HERE>` を実際の国土交通データプラットフォームの API キーに置き換えてください
2. 必要に応じて `command` を仮想環境の Python パスに変更してください
3. 拡張機能の設定でこのファイルを参照するか、内容をコピーしてください

**重要:** このファイルに実際の API キーを記入した場合は、Git にコミットしないよう注意してください。

詳細なセットアップ手順については、[VSCode セットアップガイド](../docs/vscode-setup.md) を参照してください。
