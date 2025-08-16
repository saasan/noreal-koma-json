# noreal-koma-json

存在しない漫画の1コマbot ([@noreal_koma](https://x.com/noreal_koma))
の漫画から抽出したテキストデータによみがな等の情報を追加したJSONファイル

## JSON Schemaでのバリデーション

```shell
# ajv-cliのインストール
sudo npm install -g ajv-cli ajv-formats
# 1ファイルのバリデーション
ajv validate -c ajv-formats -s schema.json -d 202408/noreal_koma-1822589276636303396-01-20240811200200.json
# 複数ファイルのバリデーション
find 202408 -type f -name '*.json' -print0 | xargs -0 -n1 ajv validate -c ajv-formats -s schema.json -d
```

## 空の要素を削除してJSONファイルを結合

```shell
jq -cn '
  [
    inputs
    | del(.. | select(. == {} or . == [] or . == "" or . == null))
    | .searchText = (
        (
          (.text // []) + 
          (.reading // []) + 
          (.tag // []) + 
          (.series // []) + 
          (.characters // []) + 
          ((.relatedLinks // []) | map(.title // ""))
        ) | join(" ")
      )
  ]
' 20????/*.json > merged.json
```
