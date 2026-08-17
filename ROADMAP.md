# SDayDream Music Studio 開発ロードマップ

## Phase 1（今回・完成）— preview.html
ブラウザ単体で動く作曲スタジオ。サーバー不要、インストール不要。

| 仕様書の項目 | 状態 |
| --- | --- |
| 2画面レイアウト（左：制作／右：プレビュー） | 動作 |
| ホーム画面 6メニュー | 動作 |
| AI作曲（曲名・テーマ・曲調・雰囲気・歌詞・キー・BPM・長さ） | 動作 |
| 長さ 30秒／1分／3分／5分／10分 | 動作 |
| BPM 40〜240、移調 -15〜+15 | 動作 |
| ボーカル生成（6メンバー・ソロ/デュエット/トリオ/全員） | 動作（シンセ歌唱） |
| 楽器ライブラリ（バンド6・オーケストラ14・マンドリン4） | 動作 |
| AI編曲（コード進行・メロディー・ベース・間奏・エンディング） | 動作 |
| 手動編集（音量・パン・エフェクト・楽器追加削除） | 動作 |
| プリセット11種 | 動作 |
| AIミキシング（ノイズ除去・EQ・コンプ・リバーブ・マスタリング） | 動作 |
| 波形表示・歌詞同期・楽譜表示 | 動作 |
| MVプレビュー | 動作（キャンバス生成） |
| 書き出し WAV / MIDI / プロジェクトJSON | 動作 |
| MP3 / FLAC / MP4 / MOV | Phase 2以降 |
| MV自動生成（画像・動画生成） | Phase 4 |
| YouTube自動投稿 | Phase 5 |
| AI社員連携 | Phase 3 |

### 音の作り方（Phase 1）
- 手続き型の作曲エンジン。プリセットごとのコード進行から、メロディー → ベース → ドラム → 各楽器を組み立てます。
- 歌詞1行のモーラ数からその小節の音数を決めるので、歌詞を変えるとメロディーの刻みが変わります。
- 音源は Web Audio API のシンセ。弦は鋸波＋ビブラート、管はフィルタースイープ、マンドリンはトレモロ（連続ピッキング）、ボーカルは3声デチューン＋フォルマント3バンド＋ブレスノイズ。美琴はビブラート幅を広げて演歌寄りにしています。
- 全体を OfflineAudioContext で一括レンダリングしてから再生・書き出しするので、再生位置の移動や波形描画が軽く済みます。

### 注意点
- ライブラリ保存はページを開いている間だけです（localStorage 不使用）。曲を残すときは WAV / MIDI / JSON で書き出してください。
- 10分＋オーケストラ全部入りは書き出しに数十秒かかります。音符数が上限を超えると伴奏側を自動で間引きます。
- Phase 1 のボーカルは実在の歌声の再現ではありません。本人の声を学習させる段階では同意の取得が必要で、既存曲を扱う場合は別途権利処理が必要です。

---

## Phase 2 — アプリ化（GUI運用のまま）
仕様書では Next.js + FastAPI ですが、トシさんの運用（GitHub Desktop ＋ Vercel）に合わせるなら **Vite + React + TypeScript + Tailwind + Vercel Functions** を勧めます。FastAPI を別サーバーで持つと、GUI だけでの更新が難しくなります。

```
sdaydream-music-studio/
├─ src/
│  ├─ engine/        composer.ts / instruments.ts / render.ts / exportWav.ts / exportMidi.ts
│  ├─ screens/       Home / Studio / Library / MVLibrary / Settings
│  ├─ components/    Waveform / Transport / Mixer / LyricSync / MVCanvas
│  └─ lib/           firebase.ts / types.ts
├─ api/              compose.ts（歌詞アシスト）/ tts.ts / mv.ts
└─ vite.config.ts
```
- Firebase Auth ＋ Firestore で曲を端末間で共有、音声は Firebase Storage。
- MP3 / FLAC は `@ffmpeg/ffmpeg`（WASM）でブラウザ内変換。サーバー不要です。

## Phase 3 — AI社員連携と歌詞アシスト
Vercel Functions 経由で Groq / Gemini / OpenAI を呼び、歌詞の推敲と構成案を出します。APIキーは環境変数に置き、ブラウザには渡しません。Takagi＝公開計画、Ota＝歌詞レビュー、Nakao＝書き出しログ、Shun＝反応集計。

## Phase 4 — MV自動生成
歌詞解析 → シーン割り → 画像生成 → 動画化 → 字幕 → 合成。MP4 書き出しは `ffmpeg.wasm` か、重い場合はサーバー処理に切り出します。

## Phase 5 — YouTube 投稿
YouTube Data API v3。OAuth の同意画面と審査が必要なので、他のフェーズより先に申請だけ進めておくのが安全です。
