# skills-deep-research
Deep Research Skill for Claude Code.

任意のトピックについて、構造化されたディープリサーチを行う Claude Code カスタムスキル。

## 概要

`/research <トピック>` で起動すると、以下の4フェーズで体系的な調査を実施し、ソース付きのリサーチレポートを生成する。

```
Phase 1: 計画  →  Phase 2: 調査  →  Phase 3: 検証  →  Phase 4: レポート
```

各フェーズ間でユーザーの確認を取りながら進行。

## 特徴

- **構造化ワークフロー** - 計画→調査→検証→レポートの4段階プロセス
- **エビデンスベース** - すべての主張にソースURLを付与
- **中間ファイル出力** - 調査結果・検証結果をファイルに保存し、コンテキストウィンドウの枯渇を防止
- **クロスリファレンス検証** - 複数ソース間の矛盾を検出・明示
- **日本語出力** - レポートおよびユーザー向け出力はすべて日本語

## インストール

### グローバル（全プロジェクト共通）

```bash
# スキルディレクトリにクローン
git clone <this-repo> ~/.claude/skills/deep-research
```

### プロジェクト単位

```bash
# プロジェクトルートにクローン
git clone <this-repo> .claude/skills/deep-research
```

## 使い方

Claude Code 内で以下のように呼び出します:

```
/research <調査したいトピック>
```

### 例

```
/research WebAssembly のブラウザ外ランタイム比較
/research 2025年以降の日本のスタートアップ資金調達トレンド
/research Rust vs Go バックエンド開発における性能特性
```

### 出力ファイル

調査完了後、ワーキングディレクトリに以下のファイルが生成される:

| ファイル | 内容 |
|---|---|
| `research-evidence-<topic>.md` | 各サブトピックのエビデンスカード |
| `research-verify-<topic>.md` | クロスリファレンス・検証結果 |
| `research-report-<topic>.md` | 最終レポート |

## ライセンス

MIT
