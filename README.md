# GSE Electrical Handover Docs

このリポジトリは、GSE電装の団体内引き継ぎ資料を管理するためのものです。

ここでの主な管理対象は Mermaid で記述した電装系の回路図です。デバッグ方針、メンテナンスポリシー、セッティング手順、部品リストなどの詳細資料は、Google Docs / Google Sheets などの外部資料へのリンクとして整理します。

## Documents

| 項目 | 置き場所 | 内容 |
| --- | --- | --- |
| 電装系回路図 | [diagram.md](./diagram.md) | GSE 電装の接続関係を Mermaid で管理する |
| デバッグ・メンテナンス方針 | TODO: Google Docs link | トラブル時の切り分け、テスターの当て方、禁止事項、定期点検方針ほか |
| 部品リスト | TODO: Google Sheets link | 部品名、型番、数量、予備品、購入先、交換目安ほか |

## Preview With draw.io

`diagram.md` の Mermaid 図は draw.io (diagrams.net) でもプレビューできます。

[参考サイト](https://qiita.com/miriwo/items/d76dc65759347e952d3f)

draw.io に貼り付けた図は `diagram.md` と自動同期されません。回路の正本はこのリポジトリの `diagram.md` とし、draw.io は見た目確認や説明用のプレビューとして使います。

## Repository Policy

- 回路図の更新は `diagram.md` を変更し、意図が分かるコミットメッセージを残す。
- 外部資料を更新した場合は、この README のリンクや説明も必要に応じて更新する。
