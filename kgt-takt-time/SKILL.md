---
name: kgt-takt-time
description: KGT グループ共通のタクトタイム・生産計画専門エージェント。タクトタイム計算・工程負荷分析・ボトルネック特定・OEE・キャパシティ計画・平準化（Heijunka）・負荷山積みの知識を提供する。生産計画・タクト・工程負荷・キャパシティの依頼時に自動適用。
---

# タクトタイム・生産計画専門エージェント（KGT グループ共通）

顧客要求に対して工場の生産能力を最適に配分するための知識を提供します。

---

## 1. タクトタイムの基礎

```
タクトタイム = 利用可能稼働時間 / 顧客要求数量

例:
  1日の稼働時間: 8h × 60分 = 480分
  日次必要生産数: 100個
  タクトタイム = 480 / 100 = 4.8分/個

  → この工場は4.8分以内に1個を完成させなければならない
  → サイクルタイム > タクトタイム なら納期遅延が発生する
```

```python
def calculate_takt_time(
    working_hours_per_day: float,
    breaks_minutes: float,
    daily_demand: int,
    efficiency_rate: float = 0.85,  # 設備効率（段取り・メンテ等の損失を含む）
) -> dict:
    available_minutes = (working_hours_per_day * 60 - breaks_minutes) * efficiency_rate
    takt_time = available_minutes / daily_demand if daily_demand > 0 else float("inf")
    return {
        "available_minutes": round(available_minutes, 1),
        "daily_demand": daily_demand,
        "takt_time_min": round(takt_time, 2),
        "takt_time_sec": round(takt_time * 60, 1),
        "note": f"1個あたり {takt_time:.1f}分 ({takt_time*60:.0f}秒) 以内に完成が必要",
    }
```

---

## 2. サイクルタイム vs タクトタイム

```
サイクルタイム（CT）: 実際に1個を完成させるのにかかる時間
タクトタイム（TT）:  顧客要求に基づく目標時間（1個当たり）

関係:
  CT < TT → 余裕あり（過剰生産または段取り改善に使える）
  CT = TT → ちょうど良い（理想）
  CT > TT → ボトルネック（外注・残業・設備増強を検討）

工程ごとにチェック:
  工程A: CT 3.5分 < TT 4.8分 → OK
  工程B: CT 5.2分 > TT 4.8分 → ボトルネック!
  工程C: CT 2.1分 < TT 4.8分 → 余裕あり
```

---

## 3. ボトルネック分析

```python
def find_bottleneck(process_data: list, takt_time: float) -> dict:
    """
    各工程のサイクルタイムとタクトタイムを比較してボトルネックを特定

    process_data: [{"name": str, "cycle_time": float, "capacity": int}, ...]
    """
    bottlenecks = []
    for p in process_data:
        load_rate = p["cycle_time"] / takt_time
        status = "🔴 ボトルネック" if load_rate > 1.0 else "🟡 要注意" if load_rate > 0.9 else "🔵 OK"
        bottlenecks.append({
            "process": p["name"],
            "cycle_time": p["cycle_time"],
            "load_rate": f"{load_rate * 100:.0f}%",
            "status": status,
            "action": "外注・残業・設備増強を検討" if load_rate > 1.0 else "-",
        })
    return sorted(bottlenecks, key=lambda x: float(x["load_rate"].rstrip("%")), reverse=True)
```

### ボトルネック解消の優先順位

```
1. 段取り時間短縮（SMED: 1分以内段取り替え）
   → 段取り時間を半分にすれば稼働時間が増加

2. 不良率の削減
   → 不良品分を余計に作っている → 実効稼働時間を無駄にしている

3. 多能工化・応援体制
   → ボトルネック工程に他工程の作業者を集中

4. 外注化
   → ボトルネック工程だけを外注して内製キャパを補完

5. 設備増強・追加投資
   → 1〜4 で解決できない場合の最終手段
```

---

## 4. 工程負荷山積み（ローディング）

```
目的: 日付ごとに各工程の負荷（必要工数）を積み上げて、
     超過している日・工程を把握する

計算:
  各受注の「必要工数」= 残数量 × サイクルタイム
  各受注の「1日負荷」= 必要工数 / 残稼働日数
  日別に積み上げ → キャパシティ（1日の利用可能時間）と比較

視覚化:
  横軸: 日付
  縦軸: 各工程の負荷（分）
  棒グラフで積み上げ → キャパシティラインを超えた日が過負荷

対策:
  山が高い日: 前倒しで着手・残業・外注
  山が低い日: 将来受注の前倒し着手・段取り最適化
```

---

## 5. 平準化（Heijunka）

```
目的: 生産量・品種の均等化 → 在庫・リードタイムの最小化

ランダム生産の問題:
  月: 100個（大型）/ 火: 20個（小型）/ 水: 80個（中型）
  → 材料手配・段取り・外注の負荷が毎日変わる → 非効率

平準化後:
  月〜金: 各40個（大型20 + 中型16 + 小型4）
  → 毎日同じリズム → 段取り最小 → 外注も安定発注

平準化の前提条件:
  □ 段取り時間が短い（30分以内）
  □ 材料が常に補充されている（かんばん or 安全在庫）
  □ 顧客の需要変動が吸収できる完成品在庫
```

---

## 6. 生産進捗のアラート基準

```python
from datetime import date

def check_production_pace(
    delivery_date: date,
    remaining_quantity: int,
    daily_capacity: float,  # 1日の生産可能数
) -> dict:
    today = date.today()
    days_remaining = (delivery_date - today).days

    if days_remaining <= 0:
        return {"status": "🔴 納期超過", "action": "至急出荷 or 顧客協議"}

    required_per_day = remaining_quantity / days_remaining

    if required_per_day > daily_capacity * 1.2:
        return {
            "status": "🔴 キャパ超過",
            "required_per_day": required_per_day,
            "action": "外注・残業の即時手配",
        }
    elif required_per_day > daily_capacity:
        return {
            "status": "🟡 要注意",
            "required_per_day": required_per_day,
            "action": "段取り改善・残業で対応",
        }
    return {
        "status": "🔵 正常",
        "required_per_day": required_per_day,
        "days_buffer": days_remaining - (remaining_quantity / daily_capacity),
    }
```

---

## 7. OEE（設備総合効率）

```
OEE = 時間稼働率 × 性能稼働率 × 良品率

時間稼働率 = (計画稼働時間 - 停止ロス) / 計画稼働時間
  停止ロス: 故障・段取り・材料待ち・清掃等

性能稼働率 = (実際の生産数 × 基準CT) / 稼働時間
  性能ロス: チョコ停・速度低下

良品率 = 良品数 / 生産数
  品質ロス: 不良・手直し

世界クラス目標:
  時間稼働率: ≥ 90%
  性能稼働率: ≥ 95%
  良品率:     ≥ 99.9%
  OEE:        ≥ 85%

日本の中小製造業の平均: OEE 50〜60%
  → OEE 向上の余地が大きい
```

---

## 8. 週次生産計画の立て方（タイ工場標準）

```
月曜日（計画立案）:
  1. 先週の生産実績を確認（produced_quantity vs 計画）
  2. 受注残の優先順位を確認（納期近い順）
  3. 今週の生産計画を立案（機械別・日別割り付け）
  4. 材料・外注品の入荷スケジュール確認
  5. 外注先への今週の指示

火〜木曜日（生産実施）:
  1. 朝礼で当日の生産計画確認
  2. 進捗入力（完了数量・不良数量）
  3. 計画比で遅れている場合は即日対応（残業・段取り変更）

金曜日（完了・次週準備）:
  1. 今週の生産実績を集計
  2. OEE・不良率を集計
  3. 来週の計画を作成・材料発注
  4. 外注品の入荷確認・次週分発注

優先ルール:
  🔴 残日数3日以内 → 最優先
  🟡 残日数7日以内 → 次優先
  同じ材料・同じ機種はまとめて段取り効率化
```
