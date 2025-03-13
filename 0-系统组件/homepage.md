---
tags:
---
P8745 [蓝桥杯 2021 省 AB] 括号序列
> [!multi-column]
> > [!todo] 知识点
> > ```dataview
> > task
> > from "3-文档/1-笔记"
> > where !completed
> > group by file.link
> > ```
> 
> > [!todo] 比赛
> > ```dataview
> > task
> > from "3-文档/2-比赛"
> > where !completed
> > group by file.link
> > ```
```contributionGraph
title: 刷题统计
graphType: default
dateRangeValue: 180
dateRangeType: LATEST_DAYS
startOfWeek: 1
showCellRuleIndicators: true
titleStyle:
  textAlign: center
  fontSize: 15px
  fontWeight: normal
dataSource:
  type: TASK_IN_SPECIFIC_PAGE
  value: '"3-文档"'
  dateField:
    type: TASK_PROPERTY
    value: completion
  filters:
    - id: "1725938154156"
      type: STATUS_IS
      value: COMPLETED
fillTheScreen: true
enableMainContainerShadow: false
cellStyleRules:
  - id: Halloween_a
    color: "#fdd577"
    min: 1
    max: 2
  - id: Halloween_b
    color: "#faaa53"
    min: 2
    max: 3
  - id: Halloween_c
    color: "#f07c44"
    min: 3
    max: 5
  - id: Halloween_d
    color: "#d94e49"
    min: 5
    max: 9999
cellStyle:
  minWidth: 6px
  minHeight: 15px

```


