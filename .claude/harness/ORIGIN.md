# ハーネスのコピー元

このリポジトリのハーネス（`.claude/`・`scripts/harness/`・`.github/` のテンプレートなど）は、次のリポジトリからコピーして直したもの。

| 項目 | 値 |
| --- | --- |
| コピー元 | https://github.com/sgmt-tmy/type-chat |
| コミット | `bc00fd932e0d722050d1387ab91980d25710b607` |
| コピーした日 | 2026-10-12 |

コピー元の改善を持ち帰るときは、このコミットから `type-chat` の差分を取る（`git diff bc00fd9..HEAD -- .claude scripts/harness`）。

## 持ってこなかったもの

- `.codex/`・`.agents/`・`.cursor/`・`AGENTS.md`（Codex・Cursor は併用しない）
- `.github/workflows/claude.yml`（`@claude` で起動するワークフロー。必要になったら入れる）
- アプリの実装（`src/`）、`docs/specs/` の個別の仕様書、`docs/decisions/` の個別のADR、Next.js・SQLite関連の設定

## このリポジトリで直したもの

- エージェント定義とテストの固定値の `type-chat` を `crypto-portfolio` に置き換えた
- `claude.yml` を前提にした文言を、「入れた場合に備えた規約」に書き換えた
- `.claude/settings.json` の `deny` に、`wrangler` のデプロイ・Secret・マイグレーション・`--remote` 実行などを加えた
- `package.json`・`tsconfig.json`・`vitest.config.ts`・`eslint.config.js` を、Next.js 前提から外した最小構成にした
- `.gitignore` に、実値が入りうるファイル（`.dev.vars`、実データのCSVなど）を加えた

## まだ直していないもの

- `.claude/harness/gate-rules.json` は `type-chat` のまま。Cloudflare向けの追加（`wrangler.toml`、`migrations/**`、`deploy.yml`、Workers の `env.XXX` 参照など）は、別のPRで入れる
- `.claude/harness/conventions.md` の中の `type-chat` 時代のIssue番号（#45 など）は、経緯としてそのまま残している
