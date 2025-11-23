# Canva MCP使用ベストプラクティス

**作成日**: 2025-11-23
**バージョン**: 1.0
**目的**: Canva MCPを使った効果的なサムネイル生成の知見を共有

## 実証された成功パターン

### 基本原則

**「内容を伝え、デザインは任せる」**

Canva AIは、詳細なデザイン指示よりも、コンテンツの本質を理解して独自のデザインを生成する方が優れた結果を出します。

### クエリ構成の黄金律

```markdown
✅ **含めるべき情報**:
- 記事タイトル（日本語と英語）
- 対象読者・ペルソナ
- 記事の目的・価値提案
- 具体的なトピック・ポイント（5つなど）
- トーン＆マナー（初心者向け、専門的、親しみやすい等）
- ブランドカラー（色コードではなく「note.com green」程度）

❌ **避けるべき指示**:
- 詳細な色コード指定（#41C9B4など）
- フォントサイズの指定（72pt、48ptなど）
- レイアウトの詳細指示（上から30%の位置など）
- 要素の配置指示（中央揃え、左寄せなど）
- 装飾要素の細かい指定
```

### 成功したクエリ例

```
Article title: "noteで読まれる記事の書き方 - 5つのポイント" (How to Write Articles That Get Read on note - 5 Key Points)

This is a how-to guide for note.com beginners who are just starting to write articles. The article teaches 5 essential points for writing engaging articles that readers will actually read on the note platform.

Key topics covered:
- Writing effective titles
- Structuring articles for readability
- Creating engaging introductions
- Using appropriate formatting
- Adding compelling conclusions

The article aims to help beginners improve their writing skills and increase their article engagement on note.com.
```

**なぜ成功したか**:
- 記事の内容と目的が明確
- 対象読者が具体的
- 5つのトピックが列挙されている
- デザイン指示は一切なし
- 適度な長さ（長すぎず短すぎず）

## 失敗パターンと教訓

### パターン1: 詳細すぎるデザイン指示

```markdown
❌ 失敗例:
"Create a thumbnail with:
- Main Color: #41C9B4 (note official green)
- Accent Color: #FF6B35 (orange)
- Background: #F5F5F5 (light gray)
- Main Copy: '5つのポイント' in 72-80pt
- Number '5' in 100-120pt orange
- Sub Copy: 'noteで読まれる記事に' in 36-42pt green
- 5 checklist icons arranged horizontally
- Circular accent shapes with 20-30% opacity
..."

結果: 生成されたが、ユーザー評価「どれも微妙」
理由: AIの創造性が制限され、テンプレート的なデザインになった
```

### パターン2: 日本語での長文指示

```markdown
❌ 失敗例:
詳細な日本語仕様書をそのまま渡す（250行以上）

結果: status: "failed"
理由: クエリが長すぎ、処理できなかった
```

### パターン3: シンプルすぎる指示

```markdown
❌ 失敗例:
"noteで読まれる記事の書き方 - 5つのポイント

note初心者向けのハウツー記事です。"

結果: status: "failed"
理由: "Common queries will not be generated" - 一般的すぎて生成不可
```

## Canva generate-design API使用ガイド

### 推奨パラメータ設定

```javascript
{
  "design_type": "youtube_thumbnail",  // 1280x720に近いので最適
  "query": "[内容中心のクエリ、300-500語程度]",
  "user_intent": "[簡潔な目的説明]"
}
```

### design_type選択ガイド

| サイズ要件 | 推奨design_type | 備考 |
|-----------|----------------|------|
| 1280×670px | youtube_thumbnail | 最も近い（1280×720） |
| 1200×630px | facebook_post | OGP標準サイズ |
| 正方形 | instagram_post | 1:1比率 |

**リサイズ戦略**:
1. まず近いサイズで生成
2. create-design-from-candidateで確定
3. resize-designで正確なサイズに調整

## ファイルサイズ最適化

### 問題: エクスポート時のファイルサイズ超過

```
仕様: 1MB以下推奨
実際: 1.4MB（140%オーバー）
```

### 解決策

#### 方法1: エクスポート品質の調整（未検証）

```javascript
{
  "format": {
    "type": "png",
    "export_quality": "regular",  // "pro"ではなく"regular"を使用
    "lossless": false  // 可逆圧縮を無効化
  }
}
```

#### 方法2: JPG形式の使用（推奨）

```javascript
{
  "format": {
    "type": "jpg",  // PNGではなくJPG
    "quality": 85,  // 品質を85%程度に設定
    "export_quality": "regular"
  }
}
```

#### 方法3: エクスポート後の圧縮

```bash
# ImageMagickを使用した圧縮
convert input.png -quality 85 -define png:compression-level=9 output.png

# または pngquantを使用
pngquant --quality=80-90 input.png --output output.png
```

### 推奨ワークフロー

```markdown
1. PNGでエクスポート（export_quality: "regular"）
2. ファイルサイズチェック
3. 1MBを超える場合:
   a. JPG形式で再エクスポート（quality: 85）
   b. または圧縮ツールを使用
4. 最終確認
```

## エージェント実装への反映

### canva-thumbnail-generatorの改善ポイント

#### 1. Phase 2の具体化

```markdown
### Phase 2: Canva MCPによる生成（改善版）

**ステップ1: 記事内容の抽出**
- 記事タイトル（日本語・英語）
- 対象読者
- 記事の目的・価値
- 具体的トピック（箇条書き）
- トーン＆マナー

**ステップ2: クエリ構築**
```
Article title: "[日本語タイトル]" ([English Title])

This is a [記事タイプ] for [対象読者] who [読者の状況].
The article [記事の目的].

Key topics covered:
- [トピック1]
- [トピック2]
- [トピック3]
...

The article aims to [ゴール].

[ブランドカラーがある場合のみ]
Please consider using [ブランド名] brand colors.
```

**ステップ3: 生成実行**
- design_type: "youtube_thumbnail"（1280×670に最も近い）
- 4つの候補から選択を促す
- ユーザーフィードバックに基づいて再生成も可

**ステップ4: サイズ調整**
- create-design-from-candidateで確定
- resize-designで1280×670pxに調整
```

#### 2. ファイルサイズ最適化の追加

```markdown
### Phase 3.5: ファイルサイズ最適化（新規追加）

**ステップ1: 初回エクスポート**
- format: PNG, export_quality: "regular"

**ステップ2: サイズチェック**
```bash
file_size=$(stat -f%z "$file_path")
if [ $file_size -gt 1048576 ]; then
  echo "ファイルサイズが1MBを超過: ${file_size} bytes"
fi
```

**ステップ3: 最適化実行**
- 1MB超過の場合:
  1. JPG形式で再エクスポート（quality: 85）
  2. またはPNG圧縮ツールを使用
```

#### 3. 保存先パスの修正

```markdown
❌ 旧: specs/contents/[project]/assets/thumbnails/
✅ 新: content/assets/thumbnails/

理由: 実際のプロジェクト構造に合わせる
```

## 品質チェックリスト（更新版）

```markdown
### 生成品質
- [ ] 4つの候補が生成された
- [ ] ユーザーが候補を選択した
- [ ] デザインが記事内容と整合している

### 技術仕様
- [ ] サイズ: 1280×670px（正確に）
- [ ] ファイルサイズ: 1MB以下
- [ ] 形式: PNG（推奨）またはJPG（85%品質）
- [ ] Canva編集URLが保存されている

### プラットフォーム適合性
- [ ] note OGP要件を満たす
- [ ] モバイル表示で可読性がある
- [ ] 3秒で記事価値が伝わる

### メタデータ
- [ ] 記事ファイルのメタデータ更新完了
- [ ] サムネイルパスが記録されている
- [ ] 生成日時が記録されている
```

## 今後の改善提案

### 短期的改善（即実装可能）

1. **クエリテンプレートの整備**
   - 記事タイプ別のクエリテンプレート作成
   - ハウツー記事、体験談、解説記事など

2. **ファイルサイズ自動最適化**
   - エクスポート後の自動チェック
   - 1MB超過時の自動圧縮

3. **エージェント手順の具体化**
   - 抽象的な記述を実装可能な手順に変更
   - 実際のAPI呼び出し例を追加

### 中期的改善（検証が必要）

1. **A/Bテスト機能**
   - 複数のクエリアプローチでバリエーション生成
   - エンゲージメントデータと照合

2. **デザインパターンライブラリ**
   - 成功したデザインのパターン分類
   - 記事タイプ別の推奨アプローチ

3. **自動品質評価**
   - 生成された候補の自動スコアリング
   - モバイル可読性の自動チェック

## 関連ドキュメント

- `agents/canva-thumbnail-generator.md` - エージェント定義
- `steering/note-platform-optimization.md` - noteプラットフォーム最適化
- `templates/content/ASSETS_STRUCTURE.md` - アセット管理構造

## バージョン履歴

- **1.0** (2025-11-23): 初版作成 - 実際のサムネイル生成経験から得られた知見をまとめ
