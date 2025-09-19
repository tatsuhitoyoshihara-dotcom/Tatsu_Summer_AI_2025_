# Tatsu_Summer_AI_2025_


# ALL_CODE - ライブ配信データ収集・分析システム

このリポジトリは、複数のライブ配信プラットフォーム（YouTube、Twitch、Pococha）からデータを収集し、LLM（大規模言語モデル）を使った質疑応答（QA）システムを構築するためのコードです。

## 概要

本システムは、ライブ配信における音声とチャットの両方を分析対象とし、以下の機能を提供します：

1. **配信データの収集** - 音声、チャット、メタデータの自動収集
2. **データ前処理** - 10分間のチャンクへの分割と整形
3. **音声のテキスト化** - WhisperによるAIの書き起こし
4. **LLMを使った質疑応答** - 配信内容に基づく問題生成と回答評価

## プラットフォーム別フォルダー構成

### 📂 YouTube/
YouTube Live配信からのデータ収集システム

**ファイル:**
- `decide_what_live_to_collect.ipynb` - 収集対象配信の選択・決定
- `collect_audio_and_chat.ipynb` - 音声・チャットデータの並列収集
- `cyusyutu_ready_chat_audio.ipynb` - 音声・チャットの準備・前処理
- `for_kakiokoshi.ipynb` - 音声書き起こし処理（Whisper使用）
- `check_kakiokoshi_is_done_file.ipynb` - 書き起こし完了状況の確認

**主な機能:**
- YouTube APIを使った配信検索と選択
- 音声ダウンロード（yt-dlp使用）
- チャットログの取得
- WhisperによるAI音声書き起こし

### 📂 Twitch/
Twitch配信からのデータ収集・処理システム

**ファイル:**
- `decide_what_live_to_collect.ipynb` - 収集対象配信の決定（APIベース）
- `collect_audio_and_chat.ipynb` - 音声・チャットの並列収集（シャード対応）
- `create_10min_chunks.ipynb` - 10分チャンクの作成と配信
- `Finally_doing_llmqa.ipynb` - LLMを使った質疑応答システム

**主な機能:**
- Twitch Helix APIによる配信検索
- 分散処理による大規模データ収集（シャード機能）
- 高信頼性の収集（レジューム機能、心拍によるロック管理）
- 10分間隔でのテキストチャンク化
- Gemini 2.5 Proを使ったMCQ（多項選択問題）生成・回答

**技術的特徴:**
```python
# 分散処理の設定例
RUN_ID = "20250910_190657"
SHARD_ID = 0  # 0-9のワーカーで並列処理
SHARD_COUNT = 10
```

### 📂 Pococha 2/
Pococha配信からのデータ処理・LLM実験システム

**ファイル:**
- `create_vod_list_csv.ipynb` - VODリストの作成・管理
- `split_data_into_10min_chunks.ipynb` - 10分チャンクへのデータ分割
- `pococha_final_QA.ipynb` - 高度なLLM質疑応答実験（条件分岐ロジック付き）
- `failed_but_need_run.ipynb` - 選択肢シャッフル機能付きLLM評価

**主な機能:**
- チャット・音声書き起こしの組み合わせ分析
- 複数LLMモデル（LLM1〜LLM9）による段階的評価
- 条件分岐ロジック（正解時のみ次段階実行）
- 選択肢のランダムシャッフル（安定乱数使用）
- 詳細なメトリクス計算と分布分析

## 主要な技術スタック

### データ収集
- **YouTube**: yt-dlp, YouTube Data API
- **Twitch**: Twitch Helix API, TwitchDownloaderCLI
- **Pococha**: GCSからのデータ読み込み

### AI・機械学習
- **音声書き起こし**: OpenAI Whisper (tiny model)
- **LLM**: Google Vertex AI (Gemini 2.5 Pro)
- **データ処理**: pandas, numpy

### インフラ・ストレージ
- **クラウドストレージ**: Google Cloud Storage (GCS)
- **並列処理**: マルチシャード、心拍ベースロック
- **レジューム機能**: 中断・再開対応

## 使用方法

### 1. 環境設定

```bash
# 必要なライブラリのインストール
pip install google-cloud-storage pandas tqdm yt-dlp requests pytz vertexai gcsfs
```

### 2. 認証情報の設定

各プラットフォームのAPIキーを設定：

```python
# Twitch API
CLIENT_ID = 'ここに機密コードを入れます'
CLIENT_SECRET = 'ここに機密コードを入れます'

# Google Cloud (Vertex AI)
PROJECT_ID = "your-project-id"
LOCATION = "us-central1"
```

### 3. プラットフォーム別の実行

#### YouTube
1. `decide_what_live_to_collect.ipynb` - 収集対象を決定
2. `collect_audio_and_chat.ipynb` - データ収集実行
3. `for_kakiokoshi.ipynb` - 音声書き起こし
4. `check_kakiokoshi_is_done_file.ipynb` - 完了確認

#### Twitch
1. `decide_what_live_to_collect.ipynb` - 対象配信の決定
2. `collect_audio_and_chat.ipynb` - 分散収集実行（複数シャードで並列）
3. `create_10min_chunks.ipynb` - 10分チャンク作成
4. `Finally_doing_llmqa.ipynb` - LLM QA実験

#### Pococha
1. `create_vod_list_csv.ipynb` - データリスト作成
2. `split_data_into_10min_chunks.ipynb` - チャンク分割
3. `pococha_final_QA.ipynb` または `failed_but_need_run.ipynb` - LLM実験

## カスタマイズのポイント

### 1. 収集対象の調整

```python
# 収集件数・条件の変更
TARGET_GAME_NAME = "Just Chatting"  # Twitch
TARGET_LANGUAGE = "ja"
MAX_RESULTS = 500
```

### 2. チャンクサイズの変更

```python
# 10分 → 任意の時間に変更
CHUNK_SECONDS = 10 * 60  # 10分間
```

### 3. LLMモデルの変更

```python
# 使用するモデルの変更
MODEL_NAME = "gemini-2.5-pro"  # または他のモデル
TEMPERATURE = 0  # 生成の随機性
```

### 4. 分散処理の調整

```python
# 並列度の調整
SHARD_COUNT = 10  # 並列ワーカー数
HEARTBEAT_SEC = 60  # 心拍間隔
```

## 注意事項

### データ収集について
- **API制限**: 各プラットフォームのAPI制限を遵守してください
- **著作権**: 収集したデータの利用は適切な権利処理を行ってください
- **プライバシー**: チャットデータには個人情報が含まれる可能性があります

### 技術的制約
- **ストレージ**: 大量のデータを処理するため、十分なGCSストレージ容量が必要
- **処理時間**: 音声書き起こしやLLM処理には時間がかかります
- **料金**: Vertex AI、GCSの利用料金が発生します

### ファイルアクセス問題
注：一部のYouTubeフォルダーファイルにアクセス権限の問題があります。実行前にファイルの権限を確認してください。

## メンテナンス・トラブルシューティング

### よくある問題

1. **認証エラー**: API キーが正しく設定されているか確認
2. **権限エラー**: GCSバケットへのアクセス権限を確認
3. **レート制限**: API呼び出し頻度を調整
4. **メモリ不足**: チャンクサイズやバッチサイズを調整

### 吉原のコメント ###

上の内容は、全てのコードをcursorに読み込ませて、コードの概要を示したものです。


今回のインターンでは、時間と計算資源のの制約上、TwitchとYouTubeの分析において、それぞれ収集したデータ量、分析で利用したデータ量は小規模になります。

Twitchは10のworker.ipynbを並列で動かすため頻繁にkernelが落ちてしまい、他の作業を優先するためデータ収集期間は3日間でした。またデータ収集のコードを走らせるタイミングも日毎に一致しない＆一部のkernelが落ちた結果、予定していたデータ収集の計画通りに進まず、その一部のみが収集された結果となりました。

また、YouTubeはbot対策のため、これまた20分ごとにcookieを発行して絶対パスを貼り換え続けるという作業が必要であるため、一回のみ、200件弱のみを収集しました。
なお、YouTubeには研究者用のAPIがあり、それ以外の非公式のツールでデータをダウンロードを収集することを禁じていますので、今回のコードの内容も、規約違反になってしまうかもしれません。

そのため論文に載せるための分析を行う際には、githubに載せたコードを使う/参考にして、最後行う必要があると思います。基本的にはAPIを発行して、パスを再設定すれば、データ収集コードは問題なく動作すると思います。

このコードを動かすためには、twitchとyoutubeの両方において、APIのkeyを取得する必要があります。課金要素は一切ありません。また、cookieも取得する必要があります。私は、新しいクリーンなgoogle accountを作成し（保証用のメルアドには個人的なアドレスを入力）、そしてgoogle chromeでGet cookies.txt LOCALLYの拡張機能を用いてcookieを取得していました。なおYouTubeのコードを動かすのは、DeNAさんのパソコンでは（youtubeにアクセスが）出来ないため）困難であると考えています。そのため私用のパソコンを使うことを想定しています。そのためには、ディスクの容量を200GBほど空けておく必要があると思います（約200件のライブ配信でaudio+comment=40GBほど、5回収拾で200GB）。そのデータをgdriveに入れて、会社のパソコンに転送すると良いと思います。

今回のコードではYouTubeとTwitchの書き起こしで、Open AI Whisper(base model)を使いました。LLM QAの(コメントのみを読んだLLMによる)最終的な正答率は、この音声データの書き起こし精度に大きく依存すると考えています。Pocochaの音声テキストはノイズが小さくなく、それゆえに問題を生成するとき、コメントの内容のみを読んで答えられる問題が増えたものと認識しております。もし音声の書き起こし精度が高い場合、我々の結果の8-9割の精度は、もっと落ちるものと考えます。当然、正答率は、コメントと音声書き起こしのテキスト量の比率にも依存すると思います。（安達さんから頂いた）Pocochaの書き起こしですが、Open AI Whipserを使っている場合には、もう一段階高いレベルのモデルを使用することを検討しても良いと考えました。


