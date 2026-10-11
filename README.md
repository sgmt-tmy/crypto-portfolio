# crypto-portfolio

暗号資産取引所（GMOコイン・bitbank）の残高・取引・価格を自動で収集し、総額・内訳・推移・損益をスマホのWebとClaude（リモートMCP）から読める個人用システム。

> 現在は開発環境の整備段階。機能は今後追加する。

> **このリポジトリは public です。** 実値（APIキー・Secret・残高・取引額・口座情報など）はどこにも書かないでください。

## 必要なもの
- Node.js（LTS）

## セットアップ
```bash
npm ci
npm run setup:hooks   # mainへの直接pushを防ぐGitフック。クローンするたびに1回実行する
```

## 検証（テスト・Lint・型チェック）
```bash
npm run check
```

## ブランチ運用
mainへ直接pushしない。ブランチを切ってPRを作り、Actionsが緑になってからマージする。`main` へのマージは、Cloudflareへのデプロイの承認を兼ねる（デプロイ方法は `docs/decisions/` を参照）。

## ハーネス
開発は自律運用ハーネス（`.claude/`・`scripts/harness/`）で進める。規約は `.claude/harness/conventions.md`、コピー元は `.claude/harness/ORIGIN.md`。
