# JPCC-RAPID-PICKER
![python](https://img.shields.io/badge/python-3.11%2B-blue)  ![license](https://img.shields.io/badge/License-MIT-green)  ![ci](https://github.com/AI-NOSUKE/JPCC-RAPID-PICKER/actions/workflows/ci.yml/badge.svg)  ![release](https://img.shields.io/github/v/release/AI-NOSUKE/JPCC-RAPID-PICKER?color=orange)  

**日本語ウェブ全体の言説を「手軽に」収集できる、高速抽出ツールです。**  
Japanese Common Crawl (JPCC) データセット（2019–2023年のスナップショット公開コーパス）を対象に、キーワードを含む文章だけを抽出できます。  

---

## ✨ 特徴
- ⚡ **サクサク動く高速処理**  
  巨大なコーパスでも、まず軽くふるい落としを行い、当たり行だけ詳しく処理するため非常に速い。  

- 🎛 **シンプルな検索モード**  
  - simple … 見つかった順に指定件数まで保存  
  - all … 全件保存  
  - random … 全件処理後にランダム抽出（リザーバサンプリング）  

- 📊 **進捗ログで安心**  
  `[STAT] lines / hits` を更新表示し、長時間の処理でも進行状況が見える。  

- 📝 **結果は整形済み**  
  ヒットした本文は Unicode NFKC で正規化し、文字数をチェックしてから CSV に保存。研究利用や二次分析にそのまま使える。  

---

## ⚠️ 表記ゆれについて
RAPID は「行にそのままキーワードが含まれているか」を基準にします。  
そのため次のような**表記の揺れ**がある場合はヒットしないことがあります。  

- 全角／半角の違い  
- 英字の大文字／小文字の違い  
- カタカナ／ひらがなの表記差  
- 旧字体・異体字・特殊な漢字  
- 記号や区切り（ハイフン、長音、スペースなど）  
- 結合文字と単一文字（合成アクセント等）  

➡️ **表記ゆれまで含めて漏れなく拾いたい場合は、精密版（JPCC-PICKER）をご利用ください。**  

---

## 🔧 仕組み（概要）
- **データ形式**: JSONL（1行=1レコード）。`.jsonl` / `.jsonl.gz` に対応。  
- **アクセス**: 匿名（UNSIGNED）で AWS S3 バケット `abeja-cc-ja` から直接読み込み（AWSアカウント不要）。  
- **RAPID の流れ**: 行をざっと確認 → 当たり行だけ JSON 解釈 → 本文抽出 → 正規化・長さチェック → CSV 出力。  

---

## 📥 インストール
```bash
git clone https://github.com/AI-NOSUKE/JPCC-RAPID-PICKER.git
cd JPCC-RAPID-PICKER
pip install -r requirements.txt
```

- 依存: `boto3`（任意で `orjson` を入れると JSON 読み込みがさらに速くなります）。  
- AWS CLI や認証設定は不要。  

`requirements.txt` 例:
```
boto3==1.34.*
# optional: orjson
```

---

## ▶️ 実行方法
`jpcc_rapid_picker.py` の冒頭にあるユーザー設定を編集して実行します。

```python
# ===== ユーザー設定 =====
OUTFILE = "output.csv"                     # 出力ファイル名
KEYWORDS: list[str] = ["ももクロ","ももいろクローバーZ"]          # 抽出キーワード（複数可・部分一致）
MINL, MAXL = 100, 2000                     # 最小・最大文字数
LIMIT = 2000                               # 抽出件数（allモード時は無視）
CHUNK_SIZE = 10 * 1024 * 1024              # 非gzの行復元用チャンク（10MB）
MODE = "simple"                            # simple=見つけ次第終了, random=ランダム抽出, all=全件抽出
# ========================

# （参考：進捗ログの表示間隔。既定は 1,000）
# LOG_INTERVAL = 1_000
```

実行:
```bash
python jpcc_rapid_picker.py
```

---

## 📂 出力例
```csv
id,text,char_len
xxxx...,本文の一部...,123
yyyy...,本文の一部...,456
```

---

## ❓ Q&A
- **Q. AWS アカウントは必要ですか？**  
  A. 不要です。匿名（UNSIGNED）で公開データに直接アクセスします。  

- **Q. データは最新ですか？**  
  A. いいえ。JPCC は **2019〜2023年** のスナップショットです。  

- **Q. 表記ゆれが気になります**  
  A. RAPID はそのまま検索するため、揺れがあると拾えないことがあります。  
     漏れなく収集したい場合は **精密版（JPCC-PICKER）** をご利用ください。  

---

## 📜 ライセンス
MIT License
