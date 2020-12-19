---
title: SQL JOIN
date: 2020-05-10 18:42:05
categories: 
- 理论归纳
tags:
- 数据库
---

# SQL JOIN

## INNER JOIN

![img](SQL-JOIN\innerJoin.svg)

INNER JOIN是指目标关系的交集

```sql
SELECT 目标属性 FROM 左关系名 左别名 INNER JOIN 右关系名 右别名 on 左别名.条件属性 = 右别名.条件属性
```



## LEFT JOIN

![img](SQL-JOIN\leftJoin.svg)

LEFT JOIN是指目标关系的交集与左侧关系的并集，即显示左侧关系的全部内容，若其对应右侧关系目标值满足条件则显示，否则显示null

```sql
SELECT 目标属性 FROM 左关系名 左别名 LEFT JOIN 右关系名 右别名 on 左别名.条件属性 = 右别名.条件属性
```



## RIGHT JOIN

![img](SQL-JOIN\rightJoin.svg)

RIGHT JOIN是指目标关系的交集与右侧关系的并集，即显示右侧关系的全部内容，若其对应左侧关系目标值满足条件则显示，否则显示null

```sql
SELECT 目标属性 FROM 左关系名 左别名 RIGHT JOIN 右关系名 右别名 on 左别名.条件属性 = 右别名.条件属性
```

## FULL JOIN

![img](SQL-JOIN\fullJoin.svg)

FULL JOIN是指目标关系的并集，即显示左侧关系与右侧关系的全部内容，若其对应关系目标值满足条件则显示，否则显示null

```sql
SELECT 目标属性 FROM 左关系名 左别名 FULL JOIN 右关系名 右别名 on 左别名.条件属性 = 右别名.条件属性
```

