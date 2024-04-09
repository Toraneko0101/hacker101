# document用に作成したリポジトリ

## 導入方法
```terminal linenums="1"
python -m venv .venv
.venv\scripts\activate
pip install mkdocs
pip install mkdocs-material
```
## サーバ立ち上げ
```
mkdocs serve
※デフォルトの場合、localhost:8000で閲覧可能
```

## html変換
```
mkdocs build
```

## プロジェクト立ち上げ
```
mkdocs new <your-project-name>
```

## ドキュメントの構成を変えたい場合
```
・mkdocs.ymlに記載する
・indentがそろっていないとうまくいかないので注意
```
