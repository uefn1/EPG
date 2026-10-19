# Tigo Sports Paraguay EPG - JST 自動生成 v3.1

パラグアイの **Tigo Sports** の本物の番組表を自動取得し、日本時間(JST)に変換してGitHub Pagesで公開するプロジェクトです。APF公式日程対応。

**EPG URL (TiviMate等で使用):**
```
https://uefn1.github.io/EPG/tigo_sports_paraguay_REAL_JST.xml
```

## ✨ 特徴 - v3.1での改善

- **APF公式日程対応**: Fecha 15 (2026/10/05発表) を正確に反映
- **本物の番組表**: latribuna.com.py + abc.com.py から取得、フォールバック無しでも動作
- **日本時間自動変換**: PYT (UTC-3) → JST (UTC+9) +12時間変換
- **GitHub Actions対応**: Node.js 24対応 (checkout@v5, setup-python@v6)
- **安定版**: ubuntu-24.04固定、`.nojekyll`問題解消、`exit code 2`解消
- **TiviMate / Kodi / IPTV Simple Client** でそのまま使用可能

## 📺 現在の番組表 (Fecha 15 - APF公式)

| 日付 (PY) | 時間 (PY) | 時間 (JST) | 対戦カード | 備考 |
|---|---|---|---|---|
| 10/16 | 19:20 | 10/17 07:20 | Recoleta vs Rubio Ñu | Fecha 15 - Ricardo Gregor |
| 10/17 | 17:00 | 10/18 05:00 | Guaraní vs Nacional | Tigo Sports 100 |
| 10/17 | 19:20 | 10/18 07:20 | Olimpia vs 2 de Mayo | En vivo |
| 10/18 | 17:00 | 10/19 05:00 | Cerro Porteño vs Ameliano | La Nueva Olla - Confirmado |
| 10/18 | 19:20 | 10/19 07:20 | Trinidense vs Libertad | Simultáneo |
| 10/18 | 19:20 | 10/19 07:20 | Luqueño vs San Lorenzo | Simultáneo |
| 10/19 | 17:00 | 10/20 05:00 | Resumen Fecha 15 | Goles y Polémicas |
| 10/19 | 19:20 | 10/20 07:20 | Laboratorio Fútbol | Análisis Fecha 15 |

生成時刻: `2026/10/19 19:39 JST` - APF oficial via latribuna.com.py

## 🚀 使い方

### TiviMateでの設定

1. TiviMate → 設定 → EPG → EPGソースを追加
2. URLを入力:
   ```
   https://uefn1.github.io/EPG/tigo_sports_paraguay_REAL_JST.xml
   ```
3. 更新間隔: 24時間 / アプリ起動時に更新: ON
4. EPGを更新

### Kodi / IPTV Simple Client

アドオン設定 → EPG設定 → XMLTV URLに上記URLを入力

## 📁 ファイル構成 (最新 - スクショの構成を修正)

```
mainブランチ:
├── .github/
│   └── workflows/
│       ├── update.yml              # 自動版: 2時間毎 + 手動実行
│       └── update_manual.yml       # 手動版: ボタンを押した時だけ1回
├── auto_epg_real.py                # ★必須! v3.1 APF公式版 (ルートに置く)
├── tigo_sports_paraguay_REAL_JST.xml  # 生成されるEPG
└── README.md                       # このファイル

削除済み:
❌ auto_epg_updater.py (旧バグあり版)
❌ tigo_sports_paraguay_epg... (旧ファイル名)
❌ gh-pagesブランチ (.nojekyll問題の原因)
```

## ⚙️ GitHub Actions - 2種類のWorkflow

### 1. 自動版 `update.yml`

```yaml
on:
  schedule:
    - cron: '0 */2 * * *'  # 2時間毎 (00:00, 02:00, 04:00...)
  workflow_dispatch:        # 手動実行も可能

runs-on: ubuntu-24.04       # ubuntu-latest移行警告対策
```

- `checkout@v5` / `setup-python@v6` → Node.js 20非推奨警告を解消
- `git add -f *.xml` → .gitignoreに*.xmlがあっても強制追加
- `main`ブランチに直接コミット → .nojekyll問題なし

### 2. 手動版 `update_manual.yml`

```yaml
on:
  workflow_dispatch:
    inputs:
      reason:
        description: '実行理由 (任意)'
        required: false
        default: 'manual test'
```

- 自動実行なし、手動ボタンを押した時だけ1回
- `auto_epg_real.py` が無くても `auto_epg_real_v3_fixed.py` や `tigo_real_github_pages/` 内も探索 → exit code 2対策
- テスト用に最適

### 実行方法

1. GitHub → Actionsタブ
2. 左側から実行したいWorkflowを選択
3. 右側 `Run workflow` → Branch: main → 理由入力(手動版のみ) → Run
4. 1-2分後にEPG URLが最新になる

## 🔧 トラブルシューティング - 解決済み

### 1. `Workflow dispatch unavailable`

**原因**: Workflowファイルがデフォルトブランチ(main)に無い
**解決**: `.github/workflows/` のファイルを必ずmainブランチに置く。gh-pagesブランチでは認識されません。

### 2. `ymlが.nojekyllに置き換わる`

**原因**: 旧Workflowの
```yaml
- uses: peaceiris/actions-gh-pages@v4
  with:
    publish_dir: ./
    publish_branch: gh-pages
```
がgh-pagesブランチを丸ごと上書きし、自動で`.nojekyll`を作成。
**解決**: v3.1ではpeaceirisを完全削除。mainブランチに直接コミットする方式に変更。Settings → Pages → Source: Deploy from a branch → main / root に設定。

### 3. `exit code 2` / `Process completed with exit code 2`

**原因**: `auto_epg_real.py` がルートに無く、`auto_epg_updater.py` (旧バグあり版)しかない
**解決**: 手動版は複数パスを探索するように修正。必ず`auto_epg_real.py`をルートに置くこと。

### 4. `Node.js 20 is deprecated`

**原因**: `actions/checkout@v4`, `setup-python@v5`, `upload-artifact@v4` はNode 20用
**解決**: `checkout@v5`, `setup-python@v6` に更新 (v3.1で対応済み)

### 5. `ubuntu-latest will migrate to Ubuntu 26`

**原因**: GitHubがubuntu-latestをUbuntu 24 → 26に移行中
**解決**: `runs-on: ubuntu-24.04` に固定 (v3.1で対応済み)

## 🛠 ローカルで手動実行

```bash
pip install requests beautifulsoup4 lxml
python auto_epg_real.py
ls -lh tigo_sports_paraguay_REAL_JST.xml
head -n 30 tigo_sports_paraguay_REAL_JST.xml
```

## 📄 EPGの仕様

- 形式: XMLTV
- チャンネルID: `TigoSports.py`
- タイムゾーン: `+0900` (JST)
- 更新情報: `generator-info-date` にJSTとPYT両方を記録
- ソース: `APF oficial via latribuna.com.py - Fecha 15 confirmada`

## 🔗 関連リンク

- EPG URL: https://uefn1.github.io/EPG/tigo_sports_paraguay_REAL_JST.xml
- Tigo Sports Paraguay: https://www.tigosports.com.py
- APF公式日程ソース: https://www.latribuna.com.py/deportes/2026/10/05/a-no-parar-fechas-14-y-15-listas/
- ABC Color Fútbol: https://www.abc.com.py/deportes/futbol/

## 📄 ライセンス

MIT
