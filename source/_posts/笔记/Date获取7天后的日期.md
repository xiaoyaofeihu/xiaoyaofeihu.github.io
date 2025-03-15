---
title: Date获取7天后的日期
date: 2022-07-31 13:33:41
tags:
	- java
categories: 笔记
---

```
1、 DateUtils.addDays();
2、 private static Date getAfterDay(Date date, int diff) {
        Calendar calendar = Calendar.getInstance();
        calendar.setTime(date == null ? new Date() : date);
        calendar.add(Calendar.DATE, diff);
        return calendar.getTime();
    }
```