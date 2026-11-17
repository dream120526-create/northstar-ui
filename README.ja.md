# Quiet Axis

**あらゆる AI コーディングエージェントのための、持ち運べるデザインスキル。** 「いい感じにして」を、エージェントが実行し検証できる順序に変えます：コンテンツを分類 → ページを構造化 → 階層を作る → モーションで状態変化を説明する。

[English](README.md) · [简体中文](README.zh-CN.md) · [日本語](README.ja.md) · [Español](README.es.md)

> **作者：[@dream120526-create](https://github.com/dream120526-create) と [@appledream120526-cloud](https://github.com/appledream120526-cloud)（共同作者）。** 別の場所で見つけた場合、原本は [dream120526-create/quiet-axis](https://github.com/dream120526-create/quiet-axis) と [appledream120526-cloud/quiet-axis](https://github.com/appledream120526-cloud/quiet-axis) です。

---

## なぜ作ったか

エージェントが生成する UI は、ほぼ同じ三か所で失敗します。コンテンツが分類されていない、ページに領域がない、すべての要素が同じ重さに見える。Quiet Axis は Markdown のスキルとして、少数の硬いルール、デザイン契約、証拠ベースの合格条件を渡します。フレームワークにもベンダーにも縛られません。

**コンテキストに常駐できるほど小さく**、**出力を変えられるほど具体的**にしています。

## 含まれるもの

| ファイル | 役割 |
| --- | --- |
| `SKILL.md` | 入口：変更ティア、7 ステップ、既定値、4 つのイージング + バネ分岐、25 項目の P0 |
| `DESIGN.md` | 記入式のデザイン契約：製品事実、一文の北極星、画面ごとの主役、トークン、Do/Don't |
| `CHEATSHEET.md` | 10 分で使える 1 ページ要約 |
| `topics/motion.md` | 6 つのボタンインタラクション + 16 の UI エフェクト、実行可能なコードと SwiftUI/Compose 版 |
| `topics/spring.md` | スプリング分岐：スワイプで閉じる、ラバーバンド、スナップ、スクワークル、Liquid Glass 方針 |
| `topics/frameworks.md` | 実行可能なレシピ：CSS、Tailwind、Framer Motion、GSAP、Lottie、SwiftUI、Compose、Tauri/Electron |
| `topics/patterns.md` | 10 のページパターン、データ密度の高いページ、10 類以外の導出方法 |
| `topics/recipes.md` | トークン、シェル、ボタン、ダイアログ、Toast、スケルトン、一覧/設定/フォームの完全ページ |
| `topics/reference.md` | 48 のアンチパターン、実測トークンコントラスト、タイポグラフィ、アクセシビリティ、レビュー形式 |
| `topics/i18n.md` | 日中英混植、約物、数値、RTL、フォントスタック |
| `topics/delivery.md` | HTML / IPA / APK / EXE のビルド、署名、実機検証 |
| `references/agent-ui-skills.md` | 14 の Agent UI スキルの方法論比較 |

特徴：

- **パターン集ではありません。** 骨格は 10 個。11 個目は「内容を列挙 → 2〜3 の形を決める質問 → 5 領域に当てる → `DESIGN.md` に記録」で導出します。
- **AI っぽさの監査。** 紫青グラデーション、同じ KPI カード 4 枚、絵文字アイコン、偽指標など 18 の兆候を、Audit → Direction → Passes の順で修正。
- **意図に合うモーション。** 等速は `linear`、退出は `ease-in`、進入は `ease-out`、ページ内移動は `ease-in-out`、バネはフィードバックのみ。
- **主張ではなく証拠。** 実 hex のコントラスト、390/768/1440 のスクリーンショット、キーボード操作、280px の横溢れ、低モーション設定。
- **プラットフォーム対応、エージェント非依存。** Web、iOS、Android、デスクトップ。Claude Code、Codex、Cursor、Gemini CLI、Windsurf、Aider、普通の LLM で動作。

## インストール

フォルダをエージェントのスキルディレクトリに置くか、`SKILL.md` を読ませてください。

```bash
git clone https://github.com/dream120526-create/quiet-axis.git
```

主な配置先：

| エージェント | パス |
| --- | --- |
| Claude Code | `~/.claude/skills/quiet-axis/` または `<project>/.claude/skills/quiet-axis/` |
| Codex | `~/.agents/skills/quiet-axis/` または `<project>/.agents/skills/quiet-axis/` |
| Gemini CLI / Antigravity | `~/.gemini/config/skills/quiet-axis/` |
| Cursor | `<project>/.cursor/skills/quiet-axis/`、または `.cursor/rules` にポインタを追加 |
| Windsurf | `<project>/.windsurf/skills/quiet-axis/` |
| その他 | 任意の場所に置き、`AGENTS.md` に「UI 作業の前に `quiet-axis/SKILL.md` を読む」と 1 行追加 |

ランタイム、MCP サーバー、ビルド手順は不要です。

## 使い方

```text
quiet-axis/SKILL.md を読んで、請求設定ページを作って。
まず DESIGN.md の草案を書き、7 ステップに従って。分類は飛ばさない。
```

小さな変更：

```text
quiet-axis を使って。主ボタンの色だけ変更し、トークンのコントラスト（P0 9）だけ確認して。
```

変更ティアが定義されているので、コピー修正のたびに全面リデザインにはなりません。

## 設計原則

1. **ピクセルより論理。** 分類、構造、階層。仕上げは最後。
2. **1 画面に主役 1 つ、主操作 1 つ。** 他はすべて降格。
3. **モーションは状態変化を説明する。** 消して意味が減らないなら消す。
4. **証拠がなければ合格ではない。** スクリーンショット、計算したコントラスト、キーボード、低モーション。
5. **方向は 1 つ、一文で言い切る。** 「モダンでクリーン」は方向ではない。

## リポジトリ構成

```text
quiet-axis/
├── SKILL.md
├── DESIGN.md
├── CHEATSHEET.md
├── LICENSE
├── NOTICE
├── README.md
├── README.zh-CN.md
├── README.ja.md
├── README.es.md
├── topics/ (motion, patterns, forms, states, platforms, i18n, delivery, recipes, reference)
└── references/agent-ui-skills.md
```

## ライセンスとクレジット

**Quiet Axis License 1.0（表示—非商用）。** 複製・改変・二次創作は可能です。二人の作者のクレジット（または原本リポジトリへのリンク）が必要で、販売や有料提供はできません。全文は [LICENSE](LICENSE)。

作者：[dream120526-create](https://github.com/dream120526-create) と [appledream120526-cloud](https://github.com/appledream120526-cloud)。

第三者の方法論引用と CC BY 4.0 の表示：[NOTICE](NOTICE)。
