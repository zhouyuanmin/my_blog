---
title: django公共模型
categories: 后端
tags: django
date: 2020-03-08 10:30:53
updated: 2020-03-08 10:30:53
---
## django公共模型

```python
from django.db import models
from django.utils import timezone


class BaseModel(models.Model):
    """自定义Model基类:补充基础字段"""

    create_at = models.DateTimeField(default=timezone.now, verbose_name="创建时间")
    update_at = models.DateTimeField(auto_now=True, verbose_name="更新时间")
    delete_at = models.DateTimeField(null=True, default=None, verbose_name="删除时间")
    note = models.CharField(max_length=255, blank=True, default="", verbose_name="备注")

    class Meta:
        abstract = True

    def __str__(self):
        return str(self.pk)

    def set_delete(self):
        """软删除"""
        self.delete_at = timezone.now()
        self.save()

```

### 字段注意点

- 创建时间create_at最好使用默认值timezone.now，特殊需求，则可以修改创建时间
- CharField默认空字符串，不使用unique，因为容易与空字符串冲突，唯一值可以在业务层实现

