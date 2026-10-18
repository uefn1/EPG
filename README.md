# Tigo Sports Paraguay EPG - 自動更新リポジトリ

このリポジトリは毎日日本時間6時にEPGを自動更新します。

## EPG URL (このURLをTiviMateに入れる)
https://YOUR_USERNAME.github.io/tigo-epg/tigo_sports_paraguay_epg_JST.xml

※ YOUR_USERNAME と tigo-epg はあなたのリポジトリ名に置き換えてください

## ファイル構成
- auto_epg_updater.py : EPG生成スクリプト (JST変換済み)
- tigo_sports_paraguay_epg_JST.xml : 最新のEPG
- .github/workflows/update.yml : 毎日自動実行するGitHub Actions

## 更新ログの確認方法
GitHubリポジトリの Actions タブで実行履歴が見られます
