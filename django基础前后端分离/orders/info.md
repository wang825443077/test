# 我的订单

<img src="/orders/images/06我的订单.png" style="zoom:50%">

##### 1.请求方式

| 选项 | 方案 |
| ---------------- | ---------------- |
| **请求方法** | GET |
| **请求地址** | /orders/info/(?P&lt;page_num&gt;\d+)/ |

##### 2.请求参数：路径参数

| 参数名 | 类型 | 是否必传 | 说明 |
| ---------------- | ---------------- | ---------------- | ---------------- |
| **page_num** | int | 是 | 当前页码 |

##### 3.响应结果：json

```
{
   'page_orders': page_orders,
   'total_page': total_page,
   'page_num': page_num,
}
```

##### 4.后端接口定义和实现

```python
class UserOrderInfoView(LoginRequiredMixin, View):
    """我的订单"""

    def get(self, request, page_num):
        """提供我的订单页面"""
        user = request.user
        # 查询订单
        orders = user.orderinfo_set.all().order_by("-create_time")
        # 遍历所有订单
        for order in orders:
            # 绑定订单状态
            order.status_name = OrderInfo.ORDER_STATUS_CHOICES[order.status-1][1]
            # 绑定支付方式
            order.pay_method_name = OrderInfo.PAY_METHOD_CHOICES[order.pay_method-1][1]
            order.sku_list = []
            # 查询订单商品
            order_goods = order.skus.all()
            # 遍历订单商品
            for order_good in order_goods:
                sku = order_good.sku
                sku.count = order_good.count
                sku.amount = sku.price * sku.count
                order.sku_list.append(sku)

        # 分页
        page_num = int(page_num)
        try:
            paginator = Paginator(orders, 2)
            page_orders = paginator.page(page_num)
            total_page = paginator.num_pages
        except EmptyPage:
            return JsonResponse('code':400,
                                'errmsg':'订单不存在')

        return JsonResponse({
            'page_orders': page_orders,
            'total_page': total_page,
            'page_num': page_num,
        })
```

子路由为: 

```python
   # 我的订单
    re_path(r'^orders/info/(?P<page_num>\d+)/$', views.UserOrderInfoView.as_view()),
```



##### 

