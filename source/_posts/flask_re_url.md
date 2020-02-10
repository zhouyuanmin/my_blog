---
title: Flask中正则URL的实现
categories: 后端
tags: flask
date: 2020-02-18 12:14:54
updated: 2020-02-18 12:14:54
---

## Flask中正则URL的实现

app.route()中URL显式支持 string、int、float、path、uuid、any 6种类型，隐式支持正则

```python
DEFAULT_CONVERTERS = {
    "default": UnicodeConverter,
    "string": UnicodeConverter,
    "any": AnyConverter,
    "path": PathConverter,
    "int": IntegerConverter,
    "float": FloatConverter,
    "uuid": UUIDConverter,
}
```

### 第一步

写正则类，继承 BaseConverter，将匹配到的值设置为 regex 的值。

```python
from werkzeug.routing import BaseConverter


class RegexUrl(BaseConverter):
    def __init__(self, url_map, *args):
        super(RegexUrl, self).__init__(url_map)
        self.regex = args[0]
```

### 第二步

添加转换器到默认的转换字典中，并指定转换器使用时的名字为：re

```python
app = Flask(__name__)
app.url_map.converters['re'] = RegexUrl
```

### 第三步

使用转换器去实现自定义匹配规则，当前此处定义的规则是：3位数字

```python
@app.route('/user/<re("[0-9]{3}"):user_id>')
def user_info(user_id):
    return "user_id 为 %s" % user_id
```

