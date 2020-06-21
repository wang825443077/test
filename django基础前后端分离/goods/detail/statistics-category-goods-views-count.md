# 统计分类商品访问量

> 提示：
* 统计分类商品访问量 是统计一天内该类别的商品被访问的次数。
* 需要统计的数据，包括商品分类，访问次数，访问时间。
* 一天内，一种类别，统计一条记录。

<img src="/goods/images/68统计分类商品访问量.png" style="zoom:50%">

### 1. 统计分类商品访问量模型类

> 模型类定义在`goods.models.py`中，然后完成迁移建表。

```python
class GoodsVisitCount(BaseModel):
    """统计分类商品访问量模型类"""
    category = models.ForeignKey(GoodsCategory, 
                                 on_delete=models.CASCADE, 
                                 verbose_name='商品分类')
    count = models.IntegerField(verbose_name='访问量', default=0)
    date = models.DateField(auto_now_add=True, verbose_name='统计日期')

    class Meta:
        db_table = 'tb_goods_visit'
        verbose_name = '统计分类商品访问量'
        verbose_name_plural = verbose_name
```

##### 重新进行数据迁移:

```python
# 生成迁移文件: 
python manage.py makemigrations


# 进行数据迁移:
python manage.py migrate
```





### 2. 统计分类商品访问量后端逻辑

> **1.请求方式**

| 选项 | 方案 |
| ---------------- | ---------------- |
| **请求方法** | POST |
| **请求地址** | /detail/visit/(?P&lt;category_id&gt;\d+)/ |

> **2.请求参数：路径参数**

| 参数名 | 类型 | 是否必传 | 说明 |
| ---------------- | ---------------- | ---------------- | ---------------- |
| **category_id** | string | 是 | 商品分类ID，第三级分类 |

> **3.响应结果：JSON**

| 字段 | 说明 |
| ---------------- | ---------------- |
| **code** | 状态码 |
| **errmsg** | 错误信息 |

> **4.后端接口定义和实现**，
* 如果访问记录存在，说明今天不是第一次访问，不新建记录，访问量直接累加。
* 如果访问记录不存在，说明今天是第一次访问，新建记录并保存访问量。

```python
# 导入: 
from django.utils import timezone
import datetime

class DetailVisitView(View):
    """详情页分类商品访问量"""

    def post(self, request, category_id):
        """记录分类商品访问量"""
        
        # 根据传入的 category_id 值, 获取对应类别的商品: 
        try:
            category = GoodsCategory.objects.get(id=category_id)
        except Exception as e:
            return JsonResponse('code':400,
                                'errmsg':'缺少必传参数')

        # 获取今天的日期:
        # 先获取时间对象
        t = timezone.localtime()
        # 根据时间对象拼接日期的字符串形式: 
        today_str = '%d-%02d-%02d' % (t.year, t.month, t.day) 
        # 将字符串转为日期格式: 
        today_date = datetime.datetime.strptime(today_str, '%Y-%m-%d')
        try:
            # 将今天的日期传入进去, 获取该商品今天的访问量: 
            # 查询今天该类别的商品的访问量
            counts_data = category.goodsvisitcount_set.get(date=today_date)
        except Exception as e:
            # 如果该类别的商品在今天没有过访问记录，就新建一个访问记录
            counts_data = GoodsVisitCount()

        try:
            # 更新模型类对象里面的属性: category 和 count
            counts_data.category = category
            counts_data.count += 1
            counts_data.save()
        except Exception as e:
            logger.error(e)
            return JsonResponse('code':400,
                                'errmsg':'服务器异常')
        
        # 返回: 
        return JsonResponse({'code': 0, 
                             'errmsg': 'OK'})
```

##### 子路由: 

```python
    # 记录详情页的访问量
    url(r'^detail/visit/(?P<category_id>\d+)/$', views.DetailVisitView.as_view()),
```

