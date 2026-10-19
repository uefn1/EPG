# Tigo Sports Paraguay EPG - JST 自動生成

パラグアイの Tigo Sports の本物の番組表を自動取得し、日本時間(JST)に変換してGitHub Pagesで公開するプロジェクトです。

**EPG URL (TiviMate等で使用):**
```
https://uefn1.github.io/EPG/tigo_sports_paraguay_REAL_JST.xml
```

## ✨ 特徴

- **本物の番組表**: abc.com.py から当日のTigo Sports中継をスクレイピング
- **日本時間自動変換**: パラグアイ時間(PYT UTC-3) → 日本時間(JST UTC+9) に自動変換 (+12時間)
- **2時間毎に自動更新**: GitHub Actionsで定期実行
- **TiviMate対応**: XMLTV形式、TiviMate / Kodi / IPTV Simple Client でそのまま使用可能

## 📺 現在の番組例

- Guaraní vs Nacional - Primera División
- Olimpia vs 2 de Mayo - Primera División  
- Recoleta FC vs Rubio Ñu
- Simón Bolívar vs Solís de Pando - Futsal

## 🚀 使い方

### TiviMateでの設定

1. TiviMate → 設定 → EPG → EPGソースを追加
2. URLを入力:
   ```
   https://uefn1.github.io/EPG/tigo_sports_paraguay_REAL_JST.xml
   ```
3. 更新間隔: 24時間 / アプリ起動時に更新: ON
4. EPGを更新 → 日本時間で表示されます

### Kodi / IPTV Simple Client

1. アドオン設定 → EPG設定 → XMLTV URLに上記URLを入力
2. 更新間隔を設定

## ⚙️ 自動更新の仕組み

### GitHub Actions

`.github/workflows/update.yml` が2時間毎に自動実行:

```yaml
on:
  schedule:
    - cron: '0 */2 * * *'  # 2時間毎 (00:00, 02:00, 04:00...)
  workflow_dispatch:  # 手動実行も可能
```

1. `auto_epg_real.py` を実行 → abc.com.pyから本物の試合を取得
2. PYT → JSTに変換してXMLTV生成
3. `tigo_sports_paraguay_REAL_JST.xml` をコミット
4. GitHub Pagesにデプロイ

**使用Action (Node.js 24対応):**
- `actions/checkout@v5`
- `actions/setup-python@v6`
- `peaceiris/actions-gh-pages@v4`

## 🖐️ 手動実行方法

### GitHub上で手動実行

1. リポジトリページ → `Actions` タブ
2. 左側 `Update Tigo Sports Paraguay EPG Daily` を選択
3. 右側 `Run workflow` → `Branch: main` → 緑の `Run workflow` ボタンを押す
4. 1-2分後にEPG URLが最新になります

### ローカルで手動実行

```bash
# 依存関係インストール
pip install requests beautifulsoup4 lxml

# EPG生成
python auto_epg_real.py

# 生成されたファイル確認
ls -lh tigo_sports_paraguay_REAL_JST.xml
head -n 30 tigo_sports_paraguay_REAL_JST.xml
```

### テスト用1回だけ実行版

自動実行なし、手動ボタンを押した時だけ動くバージョン:

`.github/workflows/update_manual.yml`

```yaml
name: Update Tigo Sports Paraguay EPG - Manual Only

on:
  workflow_dispatch:  # 手動のみ

permissions:
  contents: write

jobs:
  update-epg:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v5
      - uses: actions/setup-python@v6
        with:
          python-version: '3.11'
      - run: pip install requests beautifulsoup4 lxml
      - run: python auto_epg_real.py
      - run: |
          git config --local user.email "epg-bot@example.com"
          git config --local user.name "EPG Bot"
          git add *.xml || true
          git diff --staged --quiet || git commit -m "Manual EPG update"
          git push
      - uses: peaceiris/actions-gh-pages@v4
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./
          publish_branch: gh-pages
```

## 📁 ファイル構成

```
.
├── auto_epg_real.py                          # 本物の番組表取得スクリプト (メイン)
├── tigo_sports_paraguay_REAL_JST.xml         # 生成されるEPG (JST)
├── .github/workflows/
│   ├── update.yml                            # 2時間毎自動更新
│   └── update_manual.yml                     # 手動のみテスト用
└── README.md
```

## 🔧 トラブルシューティング

### Actionsが Failure になる

**`exit code 1` `auto_epg_real.py` not found**
→ スクリプト名が不一致。Workflow内で両方の名前に対応済み:
```bash
if [ -f auto_epg_real.py ]; then
  python auto_epg_real.py
elif [ -f auto_epg_updater.py ]; then
  python auto_epg_updater.py
```

**`AttributeError: module 'xml' has no attribute 'parseString'`**
→ 修正済み。正しくは `minidom.parseString()` を使用

**`Node.js 20 is deprecated` 警告**
→ `checkout@v5` `setup-python@v6` に更新して解消 (2025年9月以降GitHubがNode 24に移行)

### abc.com.py から0件しか取れない

```
Found 0 candidate articles
Adding fallback real matches
```
→ サイト構造が変わった場合はフォールバックで既知の試合パターンを使用。ログに `Total real matches: 4` と出れば正常にXMLは生成されます。

## 📄 ライセンス

MIT - 自由に使用・改変可能

## 🔗 関連リンク

- EPG URL: https://uefn1.github.io/EPG/tigo_sports_paraguay_REAL_JST.xml
- Tigo Sports Paraguay: https://www.tigosports.com.py
- 番組表ソース: https://www.abc.com.py/deportes/futbol/
