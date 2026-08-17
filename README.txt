SDayDream Music Studio — Phase 1.1（制作画面リニューアル版）

【中身】v1.2（生成エラー対策版）
index.html … 画面
app.js     … 作曲・編曲・音源・書き出しエンジン

【Vercelに上げる】
1. この2ファイルを同じフォルダに入れる
2. Vercel にフォルダごとドラッグ&ドロップ
3. Framework Preset は「Other」
4. デプロイ後 Ctrl + Shift + R で再読み込み

【PCで開く】
index.html を右クリック →「プログラムから開く」→ Chrome / Edge
（app.js を同じフォルダに置いたままにしてください）

【画面の使い方】
・左端の縦列 … 今のトラック。押すと制作設定が開きます
・中央左 LYRICS … 「書く」で歌詞入力、「同期」で再生に合わせて光ります
・中央右 SESSION … BPM・キー・拍子、コード進行、トラックミキサー
・右 REALTIME … 可視化。下は 波形／楽譜／MV の切り替え
・下 TIMELINE … 曲構成。押すとその位置へ飛びます
・PLAY / STOP / REC（RECはWAV書き出し）
