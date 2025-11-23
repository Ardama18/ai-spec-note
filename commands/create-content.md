---
description: noteコンテンツ制作オーケストレーター - 企画分析から執筆・AI最適化まで完全サイクルを管理
---
**コマンドコンテキスト**: コンテンツ制作の完全サイクル管理（要求分析→企画→執筆→AI最適化→品質保証）

think harder

## 初回必須タスク

作業開始前に以下のルールファイルを必ず読み込み、厳守してください：
- ~/.claude/plugins/marketplaces/ai-spec-driven-framework/steering/ai-optimized-content-principles.md - 全エージェント共通原則（タスク管理、品質基準、エラー対応）
- ~/.claude/plugins/marketplaces/ai-spec-driven-framework/steering/ai-content-orchestration.md - コンテンツオーケストレーション管理フロー
- ~/.claude/plugins/marketplaces/ai-spec-driven-framework/steering/omnichannel-content-context.md - プロジェクトコンテキスト・マルチプラットフォーム戦略
- ~/.claude/plugins/marketplaces/ai-spec-driven-framework/steering/content-strategy-criteria.md - コンテンツ戦略基準・企画判定マトリクス
- ~/.claude/plugins/marketplaces/ai-spec-driven-framework/steering/note-platform-optimization.md - noteプラットフォーム特化最適化
- ~/.claude/plugins/marketplaces/ai-spec-driven-framework/steering/audience-engagement.md - AI時代の読者エンゲージメント戦略

## 実行判断フロー

### 1. 現在状況の判定
指示内容: $ARGUMENTS

現在の状況を判定：

| 状況パターン | 判定基準 | 次のアクション |
|------------|---------|-------------|
| 新規コンテンツ要求 | 既存制作なし、新しいコンテンツ制作依頼 | content-analyzerから開始 |
| 制作フロー継続 | 既存企画書/スケジュールあり、継続指示 | ai-content-orchestration.mdのフローで次のステップを特定 |
| 品質最適化エラー | AI最適化不備、プラットフォーム最適化問題 | content-reviewer実行 |
| 不明瞭 | 意図が曖昧、複数の解釈が可能 | ユーザーに確認 |

### 2. 継続時の進捗確認
フロー継続の場合、以下を確認：
- 最新の成果物（コンテンツ企画書/CSR/構造設計書/投稿スケジュール/記事）
- 現在のフェーズ位置（分析/企画/執筆/最適化/品質保証）
- ai-content-orchestration.mdの該当フローで次のステップを特定

### 3. 次のアクション実行

**ai-content-orchestration.mdを必ず参照**：
- 規模別フロー（シンプル/標準/包括的）を確認
- 自律実行モードの条件を確認
- 必須停止ポイントを認識
- フローに定義された次のエージェントを呼び出す

## 📋 ai-content-orchestration.md準拠の実行

**実行前チェック（必須）**：
- [ ] ai-content-orchestration.mdの該当フローを確認した
- [ ] 現在の進捗位置を特定した
- [ ] 次のステップを明確にした
- [ ] 停止ポイントを認識した
- [ ] コンテンツ制作後の品質サイクルを理解した

**フロー逸脱禁止**: ai-content-orchestration.mdに定義されたフローから外れることは禁止。

## 🎯 オーケストレーターとしての必須責務

### コンテンツ制作時の品質サイクル管理
**絶対的ルール**：content-executor実行後は必ず以下を実行
1. content-reviewerによる品質確認
2. 次コンテンツの制作または完成報告

**省略禁止**：このサイクルを省略した場合、AI最適化・読者価値を保証できない

### AI最適化情報の伝達
aeo-optimizer・geo-optimizer実行後、content-executor呼び出し時には以下を伝達：
- AEO最適化の実装要件
- GEO最適化の実装要件
- プラットフォーム最適化との統合方法の明示

### サムネイル生成の処理
content-executor Phase 2完了後、構造化レスポンスの`status`を確認：
1. **`thumbnail_generation_pending`の場合**:
   - canva-thumbnail-generatorを呼び出し
   - `nextAction.prompt`を使用して記事情報を伝達
   - 生成結果の`status`を確認:
     - `generated`: content-reviewerへ進む
     - `error` / `escalation_needed`: ユーザーに報告（公開はブロックしない）

2. **`completed`の場合**:
   - サムネイル生成をスキップ
   - 直接content-reviewerへ進む

**重要**: サムネイル生成エラーは記事公開をブロックしない（警告のみ）。手動でのサムネイル作成を推奨する。

## 責務境界

**本コマンドの責務**: オーケストレーターとしてコンテンツ制作エージェントを適切に振り分け、完全サイクルを管理
**責務外**: 自身でのコンテンツ執筆、調査作業（Grep/Glob/Read等）

---

## コンテンツ制作専門原則（オーケストレーター・制作エージェント向け）

### 🚨 最重要原則：分析OK、制作STOP

**すべてのWrite/Edit/MultiEditツール使用前にユーザー承認が必須**

理由：ユーザーの意図と異なるコンテンツを防ぎ、正しい方向性を確保するため

### コンテンツ制作実行プロセス

#### 実行フロー（必須手順）
1. **TodoWriteでタスク分解** → なければ制作ステップに進めない
2. **Write/Edit/MultiEdit使用** → ユーザー承認が必須
3. **制作実行** → AI最適化・品質チェックエラーは完了条件を満たさない
4. **記事数カウント** → 閾値超過で自動停止

#### TodoWriteと制作の統合
**実行ルール**：
- TodoWriteなしでWriteツール使用: ルール違反として停止
  理由：タスク管理なしでは進捗追跡と品質保証ができないため

#### 制作前提条件
1. **TodoWriteのタスク** → in_progressステータスが存在すること
2. **ユーザー承認記録** → Write/Edit前に明示的承認があること
3. **AI最適化チェック結果** → AEO・GEOスコア基準未満では完了不可

#### 禁止事項（制作時）
- **TodoWriteなしのWrite使用** → ルール違反
- **承認なしWrite/Edit/MultiEdit** → 承認待ちに移行
- **AI最適化エラー残存での完了宣言** → 完了条件を満たさない

### 制作時の行動制御（失敗防止）

#### 自動停止トリガー（必ず停止）
- **5記事以上の制作検出**：即座停止、制作範囲をユーザーに報告
  理由：大規模制作は事前戦略とレビューが必要なため
- **3記事制作完了**: TodoWriteの更新を強制（更新しないと次のWriteツール使用不可）
  理由：進捗確認と方向性の再確認が必要なため

#### 品質劣化衝動対処
1. 品質問題発見 → **一時停止**
2. 根本原因分析（なぜ？を5回繰り返して真因を特定）
3. 改善計画提示
4. ユーザー承認後に修正

#### 集中時のルール無視防止
**測定可能な強制停止基準**：
- **連続品質修正2回目**: 一時停止し、根本原因分析を実施
- **Writeツール5回使用**: 制作範囲レポートの作成を強制
- **同一記事3回編集**: 企画見直し検討の強制停止

### 品質チェックの責務分担

#### 各コンテンツ制作時
content-executorが投稿スケジュールの完了条件に従って基本品質チェック実行：
- AEO最適化スコア: 基準値以上
- GEO最適化スコア: 基準値以上
- プラットフォーム最適化: 要件満足

#### 最終制作時
content-reviewerがシリーズ全体品質チェック実行：
- オーケストレーターが最終Phaseの最終タスクで呼び出し
- シリーズ全体の統合品質を保証

## コンテンツ制作エージェント連携フロー

### 企画フェーズ
1. **content-analyzer**: 要求分析・規模判定
2. **content-planner**: 企画書作成（必要に応じて）
3. **trend-researcher**: ネタ・トピック生成（大規模時）
4. **engagement-analyzer**: 読者分析・ペルソナ設定（中規模以上）
5. **writing-advisor**: ライティングルール選択（必要に応じて）

### 制作フェーズ
1. **content-executor**: 実際のコンテンツ制作実行
2. **aeo-optimizer**: AEO最適化実装
3. **geo-optimizer**: GEO最適化実装
4. **canva-thumbnail-generator**: サムネイル生成（noteプラットフォーム向け）

### 品質保証・投稿準備フェーズ
1. **content-reviewer**: 総合品質確認・改善提案
2. **content-schedule-planner**: 投稿スケジュール作成・タイミング最適化
3. **series-coordinator**: シリーズ統合管理（必要に応じて）
4. **monetization-planner**: 収益化戦略設計（有料コンテンツ時）

### 投稿実行フェーズ（publish-content.mdで実行）
1. **note-publisher**: note特化投稿・公開実行
2. **engagement-analyzer**: 初期反応分析・エンゲージメント促進

### 効果測定・改善フェーズ（analyze-content.mdで実行）
1. **analytics-checker**: 包括的効果測定・ROI分析
2. **trend-researcher**: 市場動向・改善機会分析
3. **series-coordinator**: シリーズ全体最適化提案

### エージェント管理の原則
- **完全サイクル管理**: 企画→制作→品質保証→投稿準備まで一貫管理（create-content.mdスコープ）
- **3コマンド連携**: create-content.md → publish-content.md → analyze-content.mdの順次実行
- **段階的品質確保**: 各フェーズでの品質ゲートクリア後に進行
- **柔軟な規模対応**: シンプル・標準・包括的規模に応じたエージェント選択
- **エージェント責務分離**: 各エージェントの専門性を活かした適切な役割分担

### エージェント呼び出し原則
- **段階的実行**: 各フェーズの完了確認後に次フェーズ移行
- **品質ゲート**: 各エージェントの品質基準クリア後に進行
- **柔軟調整**: 品質問題・戦略変更時の適切なエージェント再実行

## 成功パターンとリスク回避

### 成功パターン
1. **戦略的企画**: content-analyzer→trend-researcher→engagement-analyzerによる包括的企画
2. **段階的制作**: content-executorの計画的実行
3. **継続最適化**: aeo-optimizer・geo-optimizerの効果的活用
4. **品質保証**: content-reviewerによる確実な品質確認
5. **収益化統合**: monetization-plannerによる戦略的収益化設計

### リスク回避策
1. **品質劣化防止**: 各段階での品質チェック・承認プロセス
2. **方向性乖離防止**: 定期的なユーザー確認・承認
3. **AI最適化忘れ防止**: 必須エージェント実行の自動化
4. **統合品質確保**: series-coordinatorによる全体最適化
5. **3コマンド連携確保**: 投稿・分析フェーズのエージェント活用忘れ防止