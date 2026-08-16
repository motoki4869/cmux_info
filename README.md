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

## 初心者向け活用Tips

参考：[cmuxの使い方：完全ガイド（note）](https://note.com/kawaidesign/n/ne9dd5aa7d125)

### 基本の考え方

cmuxは「ウィンドウ → ワークスペース → ペイン → サーフェス」という階層構造で画面を整理する。**1案件＝1ワークスペース、案件内の個々の作業＝ワークスペース内の複数ペイン**という単位で使うのが基本の考え方。案件をまたぐときはワークスペースごと切り替える（`⌘1`〜`9`）。

### 最初に覚えるべきショートカット

すべてのショートカットは後述の「デフォルトキーボードショートカット」節に一覧があるが、最初はこれだけ覚えれば動かせる。

| ショートカット | 機能 |
| --- | --- |
| `⌘N` | 新しいワークスペースを作成 |
| `⌘1`〜`9` | ワークスペースを切り替え |
| `⌘T` | 同じペインに新しいサーフェス（ターミナル）を追加 |
| `⌘D` / `⌘⇧D` | ペインを右/下に分割 |
| `⌥⌘←→↑↓` | 隣のペインへフォーカス移動 |
| `⌘⇧O` | 直前のセッションを再度開く |

### AIエージェントを並行運用するときのレイアウト例

1つのワークスペース内でペインを役割分担すると作業が把握しやすい。

- 左ペイン：Claude Code や Codex などのAIエージェント本体
- 右上ペイン：テスト実行・開発サーバー
- 右下ペイン：ログ・差分（`⌃⌘⇧D`で差分ビューアーを開く）の確認

エージェントが入力待ちになるとリング表示・通知（`⌘I`）で知らせてくれるので、複数エージェントを立てて待ち時間を並行させやすい。ただし**複数エージェントに同じファイルを編集させる構成は競合を招きやすいため避ける**（ペイン・案件を分けてファイルの担当範囲を分離する）。

### Finder・クリップボードとの連携

ターミナル操作とFinder/クリップボードを行き来する定番コマンド。

```bash
open .                    # 現在のフォルダをFinderで開く
open -R "/path/to/file"   # ファイルをFinderで選択表示
open "$(pbpaste)"         # クリップボードにコピーしたパスをそのまま開く
```

AIエージェントが出力したファイルパスをコピーして `open "$(pbpaste)"` で即座にFinder確認する、という流れが特に使いやすい。

### 内蔵ブラウザのCookie・履歴引き継ぎ

cmuxの内蔵ブラウザ（Webサーフェス）は、初回起動時に既存ブラウザ（Arc / Chrome等）からCookieや閲覧履歴をインポートできる。これにより、GitHubなど普段使っているサービスに**再ログインする手間を省ける**のがメリット。

- アドレスバー右端の「インポート」ボタン、または初回起動時に自動でプロファイル読み込みが走る
- 「Arc から cookie + 履歴 をインポート中...」のようなポップアップが表示されている間は取り込み処理中
- Arc/Chrome側の履歴・セッションデータが多いと、数秒〜十数秒待たされることがある
- 一度インポートが完了すれば、次回以降は同じ待ち時間は基本的に発生せず、ログイン状態も引き継がれたままになる
- 処理が固まって進まない場合は、Escキーを押すか、対象のペイン/サーフェスを閉じて作り直すことでキャンセルできる

普段使っているブラウザの認証情報を引き継げる代わりに、Cookie・履歴という機微な情報を取り込む処理である点には注意する。

### パフォーマンスに気をつける使い方

負荷が上がりやすい状況と対策：

- **負荷要因**：使っていないAIエージェントの起動放置、内蔵ブラウザでの重いページ・開発者ツール多重起動、複数エージェントによる同一ファイル編集、大量ログを出すテストの放置、不要なリモート（SSH）セッションの残存
- **対策**：`cmux top` で負荷内訳を確認する、アイドル状態のエージェントは休止させる、ブラウザの非表示タブは破棄設定にする（再読み込みは発生する点に注意）

### 向いていない使い方

以下のようなワークフローには向かない（他のツールの方が適している）。

- 単一ターミナルだけで完結する軽い作業
- GUIでの承認フロー・操作履歴を重視したい場合
- Windows/Linuxと同じ操作感を求める場合
- AIにファイル編集を任せず、自分でエディタ中心に作業したい場合

### 実践の最初の一歩

1. 左ペインでCodexまたはClaude Codeを起動する
2. 右上ペインでテスト・開発サーバーを実行する
3. 右下ペインで `cmux top` を実行し、負荷を監視する
4. AIエージェントが出したパスを `open "$(pbpaste)"` でFinder確認する

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

## ブラウザ自動操作コマンド

cmuxのワークスペースに組み込まれているブラウザパネル（`[browser]`タイプのサーフェス）を、CLIから直接操作するためのコマンド群。**claude-in-chrome拡張機能とは別物。** cmux上でブラウザテスト・動作確認を行う場合は、claude-in-chromeではなくこちらの`cmux browser`コマンドを使う。

参考：[ブラウザ自動操作ドキュメント](https://cmux.com/docs/browser-automation)、[cmux-browserスキル](https://raw.githubusercontent.com/manaflow-ai/cmux/main/skills/cmux-browser/SKILL.md)、[コマンドリファレンス](https://raw.githubusercontent.com/manaflow-ai/cmux/main/skills/cmux-browser/references/commands.md)

### 対象ブラウザサーフェスの調べ方

`cmux browser`のほとんどのサブコマンドはサーフェス指定が必要（`--surface <id>`、または最初の位置引数）。まず現在のワークスペース構成を確認し、`[browser]`タイプのサーフェスIDを特定する。

```bash
cmux tree
```

出力例：

```
└── workspace workspace:4 "..." [selected] ◀ active
    ├── pane pane:9 [focused]
    │   └── surface surface:9 [terminal] ...
    └── pane pane:12
        └── surface surface:12 [browser] "新規タブ" [selected]
```

この場合、以降のコマンドは `cmux browser --surface surface:12 ...` の形で実行する。

### ブラウザパネルを開く

```bash
cmux browser open https://example.com
cmux browser open-split https://example.com --workspace <id>
```

### ページ遷移

```bash
cmux browser --surface surface:12 goto https://github.com/<user>
cmux browser --surface surface:12 navigate https://github.com/<user>
cmux browser --surface surface:12 back
cmux browser --surface surface:12 forward
cmux browser --surface surface:12 reload
```

### 現在のURL・タイトルを取得

```bash
cmux browser --surface surface:12 url
cmux browser --surface surface:12 get url
cmux browser --surface surface:12 get title
```

### ページの状態を取得（アクセシビリティスナップショット）

```bash
cmux browser --surface surface:12 snapshot
cmux browser --surface surface:12 snapshot --interactive
```

### 要素の取得・操作

```bash
cmux browser --surface surface:12 get text --selector "h1"
cmux browser --surface surface:12 find role button --name "Sign in"
cmux browser --surface surface:12 click --selector "button.submit"
cmux browser --surface surface:12 type --selector "input[name=q]" "検索語"
cmux browser --surface surface:12 press enter
```

### スクリーンショット

```bash
cmux browser --surface surface:12 screenshot --out /tmp/shot.png
```

### JavaScript実行

```bash
cmux browser --surface surface:12 eval "document.title"
```

### その他

`wait`（要素・URL・ロード状態待機）、`cookies`（Cookie操作）、`console`/`errors`（コンソールログ・エラー取得）、`dialog`（ダイアログ処理）、`tab`（タブ管理）など。全サブコマンドは以下で確認できる。

```bash
cmux browser --help
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

## デフォルトキーボードショートカット

参考：[公式ドキュメント（キーボードショートカット）](https://cmux.com/docs/keyboard-shortcuts)

### アプリ

| ショートカット | 説明 |
| --- | --- |
| `⌘,` | 設定 |
| `⌘⇧,` | すべてのcmuxウィンドウをシステム全体で表示/非表示 |
| `⌃⌥⌘.` | cmuxアクティブ時のグローバル検索 |
| `⌥⌘F` | コマンドパレット |
| `⌃N` | コマンドパレットの次の結果 |
| `⌃P` | コマンドパレットの前の結果 |
| `⌘⇧N` | 新しいウィンドウ |
| `⌃⌘W` | ウィンドウを閉じる |
| `⌃⌘F` | フルスクリーン切り替え |
| `⌘⇧O` | 直前のセッションを再度開く |
| `⌘Q` | cmuxを終了 |

### ワークスペース

| ショートカット | 説明 |
| --- | --- |
| `⌘B` | 左サイドバーの表示切り替え |
| `⌘⌥B` | 右サイドバーの表示切り替え |
| `⌘N` | 新しいワークスペース |
| `⌥⌘N` | 新しいブラウザワークスペース |
| `⌃⌘S` | 現在のワークスペースレイアウトをテンプレートとして保存 |
| `⌘O` | フォルダを開く |
| `⌘P` | ワークスペースへ移動 |
| `⌃⌘]` | 次のワークスペース |
| `⌃⌘[` | 前のワークスペース |
| `⌃⌥⌘[` | ワークスペースを上に移動 |
| `⌃⌥⌘]` | ワークスペースを下に移動 |
| `⌘[` | フォーカスを戻す |
| `⌘]` | フォーカスを進める |
| `⌘1`〜`9` | ワークスペース1〜9を選択 |
| `⌘⇧R` | ワークスペース名を変更 |
| `⌥⌘E` | ワークスペースの説明を編集 |
| `⌘;` | ワークスペースを完了としてマーク |
| `⌘⇧;` | ワークスペースのステータスを順に切り替え |
| `⌘↩` | ハイライトされたチェックリスト項目の切り替え |
| `⌃⌘G` | 新しい空のワークスペースグループ |
| `⌘⇧G` | 選択したワークスペースをグループ化 |
| `⌃⌘.` | フォーカス中のワークスペースグループを折りたたむ/展開 |
| `⌘⇧E` | 右サイドバーへのフォーカス切り替え |
| `J`/`K`/`⌃N`/`⌃P`/`⌃H`/`⌃L` | フォーカス中のサイドバー行を移動 |
| `↩` | 選択したファイルを開く／フォルダを開閉 |
| `⌘↓` | 選択したファイルを開く／フォルダを開閉（Finder風） |
| `⌘⇧W` | ワークスペースを閉じる |

### サーフェス

| ショートカット | 説明 |
| --- | --- |
| `⌘T` | 新しいサーフェス |
| `⌘⇧]` | 次のサーフェス |
| `⌘⇧[` | 前のサーフェス |
| `⌥⌘⇧[` | サーフェスを左に並べ替え |
| `⌥⌘⇧]` | サーフェスを右に並べ替え |
| `⌃⌘⇧[` | サーフェスを前のペインへ移動 |
| `⌃⌘⇧]` | サーフェスを次のペインへ移動 |
| `⌥⌘⇧←` | サーフェスを左のペインへ移動 |
| `⌥⌘⇧→` | サーフェスを右のペインへ移動 |
| `⌥⌘⇧↑` | サーフェスを上のペインへ移動 |
| `⌥⌘⇧↓` | サーフェスを下のペインへ移動 |
| `⌃1`〜`9` | サーフェス1〜9を選択 |
| `⌘R` | タブ名を変更 |
| `⌘W` | タブを閉じる |
| `⌥⌘T` | ペイン内の他のタブを閉じる |
| `⌘⇧T` | 最後に閉じたタブを再度開く |
| `⌘⇧M` | ターミナルのコピーモード切り替え |
| `⌘⇧K` | 画面をクリア |
| `⌘⇧H` | シミュレーター：ホーム |
| `⌘←` | シミュレーター：左回転 |
| `⌘→` | シミュレーター：右回転 |
| `⌘⇧A` | シミュレーター：外観切り替え／ターミナルとTextBox間のフォーカス切り替え |
| `⌘K` | シミュレーター：ソフトウェアキーボード切り替え |
| `⇧Tab` | TextBoxの送信アクションを切り替え |
| `⌥⌘⇧A` | TextBox入力にファイルを添付 |
| `⌘S` | ファイルプレビューを保存 |

### 分割ペイン

| ショートカット | 説明 |
| --- | --- |
| `⌥⌘←` | 左のペインにフォーカス |
| `⌥⌘→` | 右のペインにフォーカス |
| `⌥⌘↑` | 上のペインにフォーカス |
| `⌥⌘↓` | 下のペインにフォーカス |
| `⌘D` | 右に分割 |
| `⌘⇧D` | 下に分割 |
| `⌥⌘D` | ブラウザを右に分割 |
| `⌥⌘⇧D` | ブラウザを下に分割 |
| `⌘⇧↩` | ペインズーム切り替え |
| `⌃⌘=` | フォントサイズを拡大 |
| `⌃⌘-` | フォントサイズを縮小 |
| `⌃⌘0` | フォントサイズをリセット |
| `⌃⌘⇧=` | 分割サイズを均等化 |

### キャンバス

| ショートカット | 説明 |
| --- | --- |
| `⌃⌘C` | キャンバスレイアウト切り替え |
| `⌃⌘R` | フォーカス中のペインを表示 |
| `⌃⌘O` | 全体表示ズーム切り替え |
| `⌥⌘=` | ズームイン |
| `⌥⌘-` | ズームアウト |
| `⌘0` | 実際のサイズ |
| `⌃⌘T` | ペインをグリッドに整列 |

### ブラウザ

| ショートカット | 説明 |
| --- | --- |
| `⌘⇧L` | ブラウザを開く |
| `⌘L` | アドレスバーにフォーカス |
| `⌘[` | 戻る |
| `⌘]` | 進む |
| `⌘R` | ページを再読み込み |
| `⌘⇧R` | ページをハード再読み込み |
| `⌘=` | ズームイン |
| `⌘-` | ズームアウト |
| `⌘0` | 実際のサイズ |
| `⌥⌘I` | ブラウザ開発者ツール切り替え |
| `⌥⌘C` | ブラウザJavaScriptコンソールを表示 |
| `⌥⌘↩` | ブラウザフォーカスモードに入る |
| `⌃⌥⌘D` | ブラウザデザインモード切り替え |
| `⌘⇧G` | React Grab切り替え |

### 差分ビューアー

| ショートカット | 説明 |
| --- | --- |
| `⌃⌘⇧D` | 差分ビューアーを開く |
| `J` | 1ステップ下にスクロール |
| `K` | 1ステップ上にスクロール |
| `⌃D` | 半ページ下にスクロール |
| `⌃U` | 半ページ上にスクロール |
| `⌃N` | 下にスクロール（Emacs風） |
| `⌃P` | 上にスクロール（Emacs風） |
| `⇧G` | 差分の一番下へスクロール |
| `G G` | 差分の一番上へスクロール |
| `/` | 差分ファイル検索を開く |
| `] F` | 次の差分ファイルへジャンプ |
| `[ F` | 前の差分ファイルへジャンプ |

### 検索

| ショートカット | 説明 |
| --- | --- |
| `⌘F` | 検索 |
| `⌘⇧F` | ディレクトリ内検索 |
| `⌘G` | 次を検索 |
| `⌥⌘G` | 前を検索 |
| `⌥⌘⇧F` | 検索バーを隠す |
| `⌘E` | 選択範囲を検索語として使用 |

### 通知

| ショートカット | 説明 |
| --- | --- |
| `⌘I` | 通知を表示 |
| `⌘⇧U` | 最新の未読へジャンプ |
| `⌥⌘U` | フォーカス中の項目の未読状態を切り替え |
| `⌃⌘U` | 最も古い未読としてマークし次へジャンプ |
| `⌘⇧H` | フォーカス中のパネルをフラッシュ表示 |

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
