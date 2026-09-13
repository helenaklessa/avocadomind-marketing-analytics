# Key DAX Measures

This document highlights selected DAX measures used in the AvocadoMind Power BI project. The focus is on business decision logic rather than listing every measure in the model.

## Investment Score

The Investment Score ranks each **Channel × Campaign Type** pattern using percentile-based scoring across four dimensions.

```DAX
Investment Score =
0.40 * [Pattern ROAS Percentile]
+ 0.25 * [Pattern CR Percentile]
+ 0.20 * [Pattern Acquisition Efficiency Percentile]
+ 0.15 * [Pattern Conversion Volume Percentile]
```

**Business purpose:** create one comparable decision score that balances return, conversion quality, acquisition efficiency, and scale.

---

## Campaign Recommendation

Campaigns are classified into four strategic actions based on ROAS and conversion-volume percentiles.

```DAX
Campaign Recommendation =
VAR ROASPct = [ROAS Percentile]
VAR ConversionPct = [Conversion Volume Percentile]
RETURN
IF(
    ISBLANK(ROASPct) || ISBLANK(ConversionPct),
    BLANK(),
    SWITCH(
        TRUE(),
        ROASPct >= 0.5 && ConversionPct >= 0.5, "SCALE",
        ROASPct >= 0.5 && ConversionPct < 0.5, "TEST & SCALE",
        ROASPct < 0.5 && ConversionPct >= 0.5, "OPTIMIZE",
        "REVIEW / REDUCE"
    )
)
```

**Business purpose:** turn campaign performance into a clear action framework instead of only reporting KPIs.

---

## Target Investment Share

The top five Channel × Campaign Type patterns are allocated a target investment share proportional to their Investment Score.

```DAX
Target Investment Share =
VAR Top5ScoreTotal =
    CALCULATE(
        SUMX(
            TOPN(
                5,
                CROSSJOIN(
                    ALL(DimChannelTable[Channel]),
                    ALL(DimCampaignTable[Campaign_Type])
                ),
                [Investment Score],
                DESC
            ),
            [Investment Score]
        )
    )
RETURN
DIVIDE(
    [Investment Score],
    Top5ScoreTotal
)
```

**Business purpose:** establish a performance-based target budget mix among the strongest investment patterns.

---

## Investment Opportunity Gap

```DAX
Investment Opportunity Gap =
[Target Investment Share] - [Current Budget Share]
```

**Business purpose:** identify which strong patterns are currently underinvested relative to their target share.

---

## Recommended Additional Budget

The +20% scenario prioritizes positive opportunity gaps while limiting immediate scale-up to a maximum of **+100% of existing spend per pattern**.

```DAX
Maximum Additional Budget =
[Total Spend USD]
```

The final `Recommended Additional Budget` measure then allocates the available growth budget sequentially by opportunity rank, subject to the cap above.

**Business purpose:** avoid assigning all incremental budget to the highest-scoring pattern and introduce a practical growth-control rule.

---

## Budget Reduction Priority

```DAX
Budget Reduction Priority =
VAR Score = [Investment Score]
VAR Spend = [Total Spend USD]
RETURN
IF(
    ISBLANK(Score),
    BLANK(),
    (1 - Score) * Spend
)
```

**Business purpose:** prioritize budget cuts where weak performance coincides with high current spend.

---

## Maximum Recommended Cut

```DAX
Maximum Recommended Cut =
[Total Spend USD] * 0.60
```

The final `Recommended Budget Cut` measure reduces budget sequentially by reduction-priority rank until the total **-20% budget target** is reached.

**Business purpose:** prevent an unrealistic full shutdown of any single pattern while still achieving the required total reduction.

---

## Core KPI examples

```DAX
ROAS =
DIVIDE(
    [Total Revenue USD],
    [Total Spend USD]
)
```

```DAX
Conversion Rate =
DIVIDE(
    [Total Conversions],
    [Total Clicks]
)
```

```DAX
Cost per New Customer USD =
DIVIDE(
    [Total Spend USD],
    [Total New Customers]
)
```

These measures form the base layer used by the more advanced recommendation and budget-allocation logic.
