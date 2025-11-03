# Content Creation Templates

このディレクトリには、note.com向けコンテンツ制作で使用するテンプレートが含まれています。

## 📁 Templates Directory Structure

```
templates/
├── content/                 # コンテンツ制作テンプレート
│   ├── plan-template.md     # コンテンツ企画書テンプレート
│   ├── schedule-template.md # 投稿スケジュールテンプレート
│   └── article-template.md  # 記事作成テンプレート
├── optimization/            # 最適化チェックリストテンプレート
│   └── platform-checklist-template.md
└── README.md               # このファイル
```

## 🎯 Template Usage by Agents

### content-planner.md → plan-template.md
**用途**: コンテンツ企画書作成
**保存先**: `content/plans/[ID]-[シリーズ名]-plan.md`
**特徴**:
- AEO/GEO最適化戦略の定義
- note特化戦略の設計
- 読者ジャーニー・コンテンツマップ図表
- 成功指標・測定方法の明確化

### content-schedule-planner.md → schedule-template.md
**用途**: 投稿スケジュール作成
**保存先**: `content/schedules/[ID]-[シリーズ名]-schedule.md`
**特徴**:
- フェーズ別タスク分解
- AI最適化・note最適化のチェック項目
- 効果測定手順の明記
- リスク管理・成功指標設定

### content-executor.md → article-template.md
**用途**: 実際の記事制作
**保存先**: `content/articles/[ID]-[記事番号]-[記事名].md`
**特徴**:
- AI理解しやすい文章構造
- note最適化要素（ハッシュタグ、エンゲージメント設計）
- 品質チェックリスト統合
- モバイル最適化確認

### note-publisher.md → platform-checklist-template.md
**用途**: 投稿前最適化チェック
**保存先**: `content/optimization/[ID]-[シリーズ名].platform-checklist.md`
**特徴**:
- note.com特化最適化項目
- AI検索エンジン統合最適化
- 収益化・エンゲージメント最適化
- 効果測定・継続改善設計

## 🔄 Template Workflow Integration

### create-content.md フロー
1. **content-analyzer** → 要求分析・規模判定
2. **content-planner** → `plan-template.md`使用してコンテンツ企画書作成
3. **content-schedule-planner** → `schedule-template.md`使用して投稿スケジュール作成
4. **content-executor** → `article-template.md`使用して記事制作
5. **content-reviewer** → 品質確認・改善提案

### publish-content.md フロー
1. **note-publisher** → `platform-checklist-template.md`使用して投稿前最適化
2. **投稿実行** → note.com特化機能活用
3. **初期反応分析** → エンゲージメント促進

### analyze-content.md フロー
1. **analytics-checker** → 包括的効果測定
2. **改善提案策定** → データドリブン最適化
3. **次期戦略調整** → 継続改善サイクル

## 🎨 Template Customization

### メタデータ構造
すべてのテンプレートは統一されたメタデータ構造を使用：

```yaml
---
id: [4桁ID]              # C001, C002, etc.
series: [シリーズ名]      # side-business, ai-writing, etc.
type: [タイプ]           # plan, schedule, article, checklist
version: 1.0.0           # セマンティックバージョニング
created: [YYYY-MM-DD]    # 作成日
based_on: [参照元]       # 依存関係
---
```

### 命名規則
- **ID**: 4桁連番（C001, C002, ...）
- **シリーズ名**: ハイフン区切り小文字（side-business, ai-writing）
- **ファイル名**: `[ID]-[シリーズ名]-[タイプ].md`

### 最適化要素統合
すべてのテンプレートに以下が統合済み：
- **AEO最適化**: AI回答エンジン最適化
- **GEO最適化**: 生成AI検索最適化
- **note特化**: プラットフォーム機能活用
- **品質保証**: 段階的品質チェック体系

## 🔧 Template Maintenance

### 更新フロー
1. 実際の制作フローでの改善点発見
2. テンプレート改良提案
3. エージェント動作確認
4. バージョン更新・リリース

### 品質基準
- **実用性**: 実際の制作フローで使いやすい
- **完全性**: 必要な要素がすべて含まれている
- **整合性**: エージェント間連携が適切
- **拡張性**: 新しい要件に対応可能

---

**重要**: これらのテンプレートは create-content.md フローの完全統合を前提として設計されています。個別使用ではなく、エージェント連携での活用を想定しています。