# crypto-portfolio

暗号資産取引所（GMOコイン・bitbank）の残高・取引・価格を自動で収集し、総額・内訳・推移・損益をスマホのWebとClaude（リモートMCP）から読める個人用システム。Cloudflare Workers + D1 で動かす。
現在は開発環境の整備段階で、機能はこれから追加する。

> **このリポジトリは public。** 実値（APIキー・Secret・残高・取引額・口座情報・メールアドレス）を、コード・テスト・サンプル・Issue・PR・コミットメッセージ・ログのどこにも書かない。サンプルやテストデータは必ずダミー値にする。

## コマンド

```bash
npm run check       # lint + typecheck + 仕様書の形式チェック + test（これが通ることが完了の条件）
npm run check:specs # docs/specs/ の仕様書の形式チェックのみ
npm test            # テストのみ
npm run setup:hooks # Gitフック（main保護）を有効化する。クローン直後に1回実行する
```

## 構成

- `src/` … 実装とテスト（`*.test.ts` を同じ場所に置く）
- `docs/specs/` … 機能ごとの仕様書。`docs/decisions/` … アプリの設計判断（ADR）
- `scripts/` … ハーネスのスクリプトと、ローカル実行のスクリプト
- `.github/workflows/ci.yml` … PR時と `main` へのpush時に `npm run check` を実行する
- `.claude/` … 自律運用ハーネス（`type-chat` からのコピー。元のコミットは `.claude/harness/ORIGIN.md`）

セットアップ手順は README.md を参照。

## 設計の前提（変更しない）

- 実行基盤は Cloudflare Workers（Hono）、DB は D1 + Drizzle ORM、言語は TypeScript で統一する
- 取引所のAPIキーは**参照権限のみ**。注文・出金の権限を付けない。キーを持つのは収集Workerだけで、利用Worker（Web/MCP）にSecretを割り当てない
- 金額・損益・集計の計算はプログラムで行い、AIに計算させない
- 損益は管理会計。税務計算（確定申告）は対象外
- 設計の根拠は `docs/decisions/` のADRを参照

## 規約

- TypeScript の strict モードを維持する（`any` を足さない）
- 実装を変更したら、同じPRでテストも足す
- 変更は小さく保つ。1つのPRで1つのことだけを行う
- 実値を書かない（上記）。`.env`・`.dev.vars`・鍵ファイル・実データのCSVはコミットしない

## 作業の進め方

1 Issue = 1 ブランチ = 1 PR。すべての作業をIssueに結びつける。詳細（着手済みの判定・gate判定結果の書式）は `.claude/harness/conventions.md` の「ブランチとPR」を参照。

1. 作業するIssueを確認する。依頼にIssue番号がなければ、`.github/ISSUE_TEMPLATE/task.md` からIssueを作る（確認は不要。作ったIssueのURLは報告に含める）
2. gateを確認する。Issueのメタデータが `gate: true`、または `gate` ラベルがあれば gate のタスク。gate のタスクは、Issueに `gate:approved` ラベルが付くまでブランチを作らない（ブランチを作る直前に `gh issue view <番号> --json labels` で確認する）
3. `main` から作業ブランチを切る。名前は `feat/<Issue番号>-<slug>`、バグ修正は `fix/<Issue番号>-<slug>`（仕様書の下書き・ドキュメント・設定の変更も `feat/`）
   - 形式は `^(feat|fix)/([1-9][0-9]*)-([a-z0-9]+(?:-[a-z0-9]+)*)$`（slug は英小文字・数字・ハイフン）
   - 同じIssueにブランチを2本作らない。再試行は同じブランチ（PRがあればそのPR）で続ける
   - 仕様書の下書きは、実装とは別のIssueにする
4. 実装とテストを書く
5. `npm run check` を実行し、**通るまで自分で直す**
6. コミットし、push する
7. PRを作る（`.github/PULL_REQUEST_TEMPLATE.md` の項目を必ずすべて埋める。push・PR作成は確認なしで進めてよいが、テンプレートの記入は省略しない）
   - 「関連Issue」欄に `Closes #<Issue番号>` を1行だけ書く。番号はブランチ名の番号と一致させる
   - 「gate判定結果」欄が `task_gate: false` かつ `diff_check: gate` になるなら、PRを作らずに人に上げる
   - PRがマージされずに閉じられたら、新しいPRは作らずに人に上げる

## やってはいけないこと

- `main` へ直接pushする
- PRをマージする（人が行う）
- `--no-verify` を使う
- 生の `git config core.hooksPath` を実行する（有効化は `npm run setup:hooks` を使う。直接コマンドは値を問わず禁止）
- force push、履歴の書き換え、ブランチの削除
- `.env` や鍵ファイルを読む・作る・コミットする
- 課金が発生する操作、リポジトリの公開範囲の変更
- `wrangler deploy`・`wrangler secret`・`wrangler d1 migrations apply`・`wrangler d1 execute --remote` を実行する（デプロイは `main` へのマージで自動、Secret登録とD1マイグレーションは人が行う。ADR 0003）
- 実値（APIキー・残高・取引額・口座情報など）をリポジトリ・Issue・PRに書く

## 実装依頼のデフォルト方針

進め方の指示がない実装依頼は、以下を既定動作とする：
Issueを確認する（なければ作る）→ 関連する仕様書（`docs/specs/`・`docs/decisions/`）を読む → gateを確認する → ブランチを切る → 実装とテストを書く → `npm run check` が通るまで直す → push → PR作成（テンプレートの項目を埋める）→ URLを報告（Issueを作った場合はそのURLも）。

ただし、依頼文で「PRは作らず、コミットまでにして」のように範囲が明示された場合はそれに従う。

## 仕様と設計判断

- 仕様書は `docs/specs/`、設計判断（ADR）は `docs/decisions/` にある。書き方は `docs/README.md` を参照
- 機能の実装・変更の前に、関係する仕様書を読む。受け入れ条件をテストにする
- 仕様と食い違う変更が必要なら、実装する前に報告する。変更する場合は同じPRで仕様書も更新する
- 該当する仕様書がない機能追加は、実装せずに仕様の下書きから始めるか確認する