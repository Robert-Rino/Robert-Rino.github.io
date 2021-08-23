---
layout: post
title: "Query mongodb oplog"
excerpt:
last_modified_at: 2021-08-21
tags:
  - mongodb
---

```
db.getCollection('oplog.rs').find({}).sort({'$natural': -1})
```