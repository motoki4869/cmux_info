# cmux コマンド一覧

参考：[公式ドキュメント](https://cmux.com/docs/getting-started)、[CLI リファレンス](https://cmux.com/docs/api)

## cmuxとは

複数のAIコーディングエージェントを管理するために設計された、Ghostty ベースの軽量な macOS 用ターミナル。縦型タブ、通知パネル、ソケットベースのコントロールAPI（CLIから操作可能）を備える。

- 動作環境：macOS 14.0 以降、Apple シリコン / Intel Mac 対応
- GitHub: [manaflow-ai/cmux](https://github.com/manaflow-ai/cmux)

## インストール

### Homebrew

```bash
brew tap manaflow-ai/cmux
brew install --cask cmux
```

### DMG（推奨）

公式サイトからダウンロードして `Applications` フォルダにドラッグ。Sparkle により自動更新に対応。

CLI ツール自体は cmux に組み込まれており、cmux 内のターミナルでは自動的に使える。cmux の外（通常のターミナルなど）から使う場合はシンボリックリンクの作成が必要。

## 共通オプション

| オプション | 説明 |
| --- | --- |
| `--socket PATH` | カスタムソケットパスを指定 |
| `--json` | JSON形式で出力 |
| `--window ID` | 特定のウィンドウをターゲット |
| `--workspace ID` | 特定のワークスペースをターゲット |
| `--surface ID` | 特定のサーフェスをターゲット |
| `--id-format refs\|uuids\|both` | JSON出力の識別子形式を制御 |

## ワークスペースコマンド

### 開いているワークスペースを一覧表示

```bash
cmux list-workspaces
cmux list-workspaces --json
```

### 新しいワークスペースを作成

```bash
cmux new-workspace
```

### 特定のワークスペースに切り替え

```bash
cmux select-workspace --workspace <id>
```

### 現在アクティブなワークスペースを取得

```bash
cmux current-workspace
```

### ワークスペースを閉じる

```bash
cmux close-workspace --workspace <id>
```

## スプリット（ペイン）コマンド

### 新しい分割ペインを作成

方向は `left` / `right` / `up` / `down`。

```bash
cmux new-split right
cmux new-split down
```

### 現在のワークスペース内のサーフェスを一覧表示

```bash
cmux list-panels
```

### フォーカスされたペイン内のサーフェスを一覧表示

```bash
cmux list-pane-surfaces
```

### 特定のサーフェスにフォーカス

```bash
cmux focus-panel --panel <id>
```

## 入力コマンド

### フォーカスされたターミナルにテキスト入力を送信

```bash
cmux send "echo hello"
cmux send --surface <id> "echo hello"
```

### キープレスを送信

`enter` / `tab` / `escape` / `backspace` など。

```bash
cmux send-key enter
cmux send-key --surface <id> enter
```

## 通知コマンド

### 通知を送信

```bash
cmux notify --title "Title" --body "Body" --subtitle "Subtitle"
```

### すべての通知を一覧表示

```bash
cmux list-notifications
```

### すべての通知をクリア

```bash
cmux clear-notifications
```

## サイドバーメタデータコマンド

### サイドバーステータスピルを設定

```bash
cmux set-status build "compiling" --icon hammer --color "#ff9500"
```

### サイドバーステータスエントリを削除

```bash
cmux clear-status build
```

### ワークスペースのすべてのステータスエントリを一覧表示

```bash
cmux list-status
```

### サイドバープログレスバーを設定（0.0〜1.0）

```bash
cmux set-progress 0.5 --label "Building..."
```

### サイドバープログレスバーをクリア

```bash
cmux clear-progress
```

### サイドバーにログエントリを追加

`--level` は `info` / `progress` / `success` / `warning` / `error`。

```bash
cmux log "Build started"
cmux log --level error --source build "Compilation failed"
```

### すべてのサイドバーログエントリをクリア

```bash
cmux clear-log
```

### サイドバーログエントリを一覧表示

```bash
cmux list-log --limit 5
```

### すべてのサイドバーメタデータをダンプ

```bash
cmux sidebar-state
```

## ユーティリティコマンド

### cmux が実行中で応答しているか確認

```bash
cmux ping
```

### 利用可能なソケットメソッドとアクセスモードを一覧表示

```bash
cmux capabilities
```

### フォーカスされたウィンドウ / ワークスペース / ペイン / サーフェスのコンテキストを表示

```bash
cmux identify
```
