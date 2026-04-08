---
description: 删除文件夹下所有.DS_Store文件
title: windows删除文件夹下所有.DS_Store文件
readingTime: false
tag:
 - 前端基础
recommend: 3
---

```powershell
Get-ChildItem -Path . -Recurse -Filter .DS_Store | Remove-Item -Force
```