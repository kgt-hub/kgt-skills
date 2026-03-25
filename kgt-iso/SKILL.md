---
name: kgt-iso
description: KGT グループ共通のISO・製造業品質マネジメント専門エージェント。ISO 9001・IATF 16949・ISO 14001・5S・PDCA・トレーサビリティ・CAPA・内部監査の知識を提供する。ISO認証・品質マネジメント・監査対応の依頼時に自動適用。
---

# ISO・品質マネジメントシステム専門エージェント（KGT グループ共通）

製造業の品質マネジメントシステム（QMS）に関する規格知識を提供します。
プロジェクト固有のコードや画面への依存なく適用できます。

---

## 1. 適用規格の概要

| 規格 | 対象 | 主な要求事項 |
|---|---|---|
| ISO 9001:2015 | 全製造業（汎用） | QMS の基盤・リスク思考・プロセスアプローチ |
| IATF 16949:2016 | 自動車部品メーカー | ISO 9001 + 自動車固有要求事項 |
| ISO 14001:2015 | 環境マネジメント | 環境側面の特定・法規制順守 |
| ISO 45001:2018 | 労働安全衛生 | 労働災害・職業病の予防 |
| ISO 13485:2016 | 医療機器 | 設計管理・リスクマネジメント |

---

## 2. ISO 9001:2015 の要求事項マッピング

### 主要条項と製造現場への適用

```
§4 組織の状況
  4.1 内外の課題の理解 → SWOT分析・競合分析
  4.2 利害関係者の理解 → 顧客・規制当局・従業員

§5 リーダーシップ
  5.1 トップマネジメントのコミットメント
  5.2 品質方針
  5.3 役割・責任・権限

§6 計画
  6.1 リスクと機会への取組み
  6.2 品質目標

§7 支援（Resources）
  7.1.5 監視・測定のリソース（測定機器の校正）
  7.5 文書化した情報（記録管理）

§8 運用
  8.1 運用の計画および管理
  8.4 外部から提供されるプロセス（外注管理）
  8.5.2 識別およびトレーサビリティ
  8.7 不適合アウトプットの管理

§9 パフォーマンス評価
  9.1 監視・測定・分析・評価
  9.2 内部監査
  9.3 マネジメントレビュー

§10 改善
  10.2 不適合および是正処置（CAPA）
  10.3 継続的改善
```

### §7.5 文書化した情報（記録管理）

```
ISO 9001 が要求する主な記録:

品質記録:
  □ 検査成績書（製品ごとの寸法・外観検査結果）
  □ 不適合品処置記録
  □ 是正処置記録（CAPA）

顧客・受注関連:
  □ 顧客要求事項（PO・仕様書・図面）
  □ 変更管理記録（図面改訂・仕様変更）

生産・物流:
  □ 製造記録（工程履歴・生産数量）
  □ 出荷記録（出荷先・数量・日付）

外注管理:
  □ 供給者評価記録
  □ 入荷検査記録
  □ 外注先への発注書・不良通知

保存期間:
  製品の安全に関わる記録: 最低10年
  一般的な品質記録: 最低5年（顧客要求に準拠）
```

---

## 3. IATF 16949 固有要求事項（自動車部品）

### PPAP（生産部品承認プロセス）

```
PPAP レベル（1〜5）:
  Level 1: 保証書のみ提出
  Level 2: 保証書 + 限定的な書類
  Level 3: 保証書 + 全書類（標準）
  Level 4: 顧客指定書類
  Level 5: 顧客工場での書類確認

Level 3 提出書類（18種）:
  1. Design Records（設計記録・図面）
  2. Engineering Change Documents（変更記録）
  3. Customer Engineering Approval
  4. DFMEA（設計 FMEA）
  5. Process Flow Diagram（工程フロー図）
  6. PFMEA（工程 FMEA）
  7. Control Plan（管理計画書）
  8. MSA（測定システム解析）
  9. Dimensional Results（寸法測定結果）
  10. Material Performance Test Results
  11. Initial Process Studies（Cpk ≥ 1.67 を要求）
  12. Qualified Laboratory Documentation
  13. AAR（外観承認報告書）
  14. 生産品サンプル
  15. Master Sample
  16. Checking Aids（検査治具）
  17. CSR（顧客固有要求事項）適合記録
  18. Part Submission Warrant（PSW）
```

### APQP（先行製品品質計画）

```
Phase 1: 計画（Plan and Define Program）
  - 顧客の声（VOC）
  - 設計目標・信頼性目標
  - 品質目標

Phase 2: 製品設計・開発
  - DFMEA（設計 FMEA）
  - 設計検証計画（DVP）

Phase 3: 工程設計・開発
  - 工程フロー図
  - PFMEA（工程 FMEA）
  - 管理計画書

Phase 4: 製品・工程の妥当性確認
  - 生産試作評価
  - MSA（Gage R&R）
  - Cpk 評価

Phase 5: フィードバックと継続改善
  - 顧客サティスファクション評価
  - 教訓（Lessons Learned）
```

---

## 4. FMEA（故障モード影響解析）

```python
FMEA_RECORD = {
    "process_step": str,     # 工程名
    "failure_mode": str,     # 故障モード（何が起きるか）
    "failure_effect": str,   # 影響（何に問題が起きるか）
    "severity": int,         # 重大度 1〜10（10が最も重大）
    "failure_cause": str,    # 原因
    "occurrence": int,       # 発生頻度 1〜10
    "current_controls": str, # 現在の予防・検出管理
    "detection": int,        # 検出度 1〜10（10が最も検出困難）
    "RPN": "severity × occurrence × detection",  # リスク優先指数
    "recommended_action": str,
    "responsible": str,
    "due_date": "date",
    "revised_severity": int,
    "revised_occurrence": int,
    "revised_detection": int,
    "revised_RPN": int,
}

# RPN の評価基準:
# RPN > 100: 高リスク → 即座に改善処置を実施
# RPN 50-100: 中リスク → 改善を計画
# RPN < 50: 低リスク → 現行管理の継続
```

---

## 5. CAPA（是正処置・予防処置）

```python
CAPA_RECORD = {
    "id": int,
    "issue_date": "date",
    "source": str,            # 内部監査 / 顧客クレーム / 不良 / 工程監視
    "problem_statement": str, # 問題の明確な記述
    "root_cause": str,        # 根本原因（5 Whys または特性要因図による分析）
    "containment_action": str,# 封じ込め処置（即時対応・流出防止）
    "corrective_action": str, # 是正処置（根本原因の排除・再発防止）
    "preventive_action": str, # 予防処置（類似問題の予防）
    "responsible_person": str,
    "due_date": "date",
    "completion_date": "date",
    "verified_by": str,       # 実施後の有効性確認者
    "verification_date": "date",
    "effective": bool,        # 処置の有効性確認結果
    "horizontal_deployment": str, # 水平展開先
}
```

---

## 6. 統計的工程管理（SPC）

```
管理図の種類:
  Xbar-R 図: 少量サンプル（n=2〜5）の計量値管理
  Xbar-S 図: 多量サンプル（n≥10）の計量値管理
  p 図:     不良率の管理
  c 図:     単位当たりの欠点数

工程能力指数:
  Cp = 規格幅 / 6σ（≥1.33 で能力十分、≥1.67 で非常に優秀）
  Cpk = min((USL-μ)/3σ, (μ-LSL)/3σ)（中心ずれを考慮）

自動車部品の要求（IATF 16949）:
  初期工程能力: Cpk ≥ 1.67
  量産時: Ppk ≥ 1.67（初期）→ Cpk ≥ 1.33（安定後）

特別特性:
  ◇ 安全特性（SC: Safety Characteristic）: 100%検査 or SPC必須
  △ 重要特性（CC: Critical Characteristic）: SPC 管理推奨
```

---

## 7. 測定システム解析（MSA / Gage R&R）

```
Gage Repeatability and Reproducibility（Gage R&R）:

目的: 測定システム（人・ゲージ）のばらつきを定量化

実施方法:
  3名の測定者 × 10サンプル × 2回測定（最低限）

判定基準:
  Gage R&R（%SV）:
    < 10%: 許容（使用可）
    10〜30%: 条件付き許容（部品の重要度による）
    > 30%: 不許容（測定システムを改善）

  ndc（Number of Distinct Categories）:
    ≥ 5: 許容

分析軸:
  Repeatability: 同一作業者が繰り返し測定したばらつき
  Reproducibility: 異なる作業者間のばらつき
```

---

## 8. 内部監査

```
内部監査チェックリスト（抜粋）:

文書管理:
  □ 品質記録が識別・保管・検索可能か
  □ 廃止文書が現場から排除されているか
  □ 外部文書（顧客図面等）が管理されているか

外注管理:
  □ 承認仕入先リスト（ASL）が最新か
  □ 入荷検査記録が全件揃っているか
  □ 供給者評価が定期的に実施されているか

工程管理:
  □ 作業標準書が作業場所に掲示されているか
  □ 測定機器の校正期限が切れていないか
  □ ポカヨケが有効に機能しているか確認

変更管理:
  □ 製品・工程変更時に顧客承認を得ているか
  □ 図面改訂履歴が管理されているか

是正処置:
  □ CAPA の期限内完了率
  □ 是正処置の有効性確認が実施されているか
  □ 水平展開が実施されているか
```

---

## 9. マネジメントレビュー

```
インプット（§9.3.2）:
  a) 前回レビューの措置状況
  b) QMS に関連する内外の変化
  c) 品質目標の達成度
  d) 内部監査の結果
  e) 顧客満足度
  f) 外部提供者のパフォーマンス
  g) 工程のパフォーマンスと製品適合性
  h) 不適合と是正処置
  i) 監視・測定の結果
  j) 改善の機会

アウトプット（§9.3.3）:
  - 継続的改善の機会
  - QMS の変更の必要性
  - 資源の必要性

実施頻度: 年1回以上（IATF 16949 では年1回以上かつリスクに応じて）
```
