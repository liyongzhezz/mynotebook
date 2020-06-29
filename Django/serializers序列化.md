序列化可以将ORM查询到的`QuerySet`格式数据转化为字典数据，方便处理。



首先安装：

```bash
pip install djangorestframework
```



示例代码：

```python
from rest_framework import serializers

from secret.models import *


class SecretSerializer(serializers.ModelSerializer):
    class Meta:
        model = SecretInfo
        fields = ("id", "name", "secret", "create_time", "update_time", "description")

    def to_representation(self, instance):
        data = super().to_representation(instance)
        if not data['description']:
            data['descaription'] = ""
        return data
```



使用`serializers`模块需要从`rest_framework`中导入；`secret.models`是我的app中的model数据库定义文件。



定义一个类继承`serializers.ModelSerializer`，`model = SearchInfo`为指定数据库表类，`fields`指定需要进行序列化的字段；



`to_representation`方法指定序列化进行处理的方法，这里判断如果`description`为空则置为“”，而不是null。



使用示例代码：

```python
query_data = models.SecretInfo.objects.filter(name=query)
data = [serializers.SecretSerializer(i).data for i in query_data]
```

