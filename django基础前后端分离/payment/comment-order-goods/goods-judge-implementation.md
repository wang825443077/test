# 评价订单商品

### 1. 展示商品评价页面

<img src="/payment/images/15订单商品评价页面.png" style="zoom:30%">

> **1.请求方式**

| 选项 | 方案 |
| ---------------- | ---------------- |
| **请求方法** | GET |
| **请求地址** | /orders/comment/ |

> **2.请求参数：查询参数**

| 参数名 | 类型 | 是否必传 | 说明 |
| ---------------- | ---------------- | ---------------- | ---------------- |
| **order_id** | int | 是 | 订单编号 |

> **3.响应结果：HTML**

```
goods_judge.html
```

> **4.后端接口定义和实现**
>
> 在 orders.views.py 文件中添加如下代码:

```python
class OrderCommentView(LoginRequiredMixin, View):
    """订单商品评价"""

    def get(self, request):
        """展示商品评价页面"""
        # 接收参数
        order_id = request.GET.get('order_id')
        # 校验参数
        try:
            OrderInfo.objects.get(order_id=order_id, 
                                  user=request.user)
        except OrderInfo.DoesNotExist:
            return http.HttpResponseNotFound('订单不存在')

        # 查询订单中未被评价的商品信息
        try:
            uncomment_goods = OrderGoods.objects.filter(order_id=order_id, 
                               is_commented=False)
        except Exception:
            return http.HttpResponseServerError('订单商品信息出错')

        # 构造待评价商品数据
        uncomment_goods_list = []
        for goods in uncomment_goods:
            uncomment_goods_list.append({
                'order_id':goods.order.order_id,
                'sku_id':goods.sku.id,
                'name':goods.sku.name,
                'price':str(goods.price),
                'default_image_url':goods.sku.default_image_url,
                'comment':goods.comment,
                'score':goods.score,
                'is_anonymous':str(goods.is_anonymous),
            })

        # 渲染模板
        context = {
            'uncomment_goods_list': uncomment_goods_list
        }
        return render(request, 'goods_judge.html', context)
```

##### 子路由: 

```python
    # 订单商品评价
    url(r'^orders/comment/(?P<order_id>\d+)/$', views.OrderCommentView.as_view()),
    url(r'^orders/comment/$', views.OrderCommentView.as_view()),
```

#####   添加样式:

> 在 goods_judge.html 中添加添加样式:

```html
    <link rel="stylesheet" type="text/css" href="{{ static('css/reset.css') }}">
    <link rel="stylesheet" type="text/css" href="{{ static('css/main.css') }}">
    <script type="text/javascript" src="{{ static('js/host.js') }}"></script>
    <script type="text/javascript" src="{{ static('js/vue-2.5.16.js') }}"></script>
    <script type="text/javascript" src="{{ static('js/axios-0.18.0.min.js') }}"></script>
    <script type="text/javascript" src="{{ static('js/jquery-1.12.4.min.js') }}"></script>
```

##### 添加 js 文件:

> 在 goods_judge.html 中添加如下代码:

```html
<script>
    var skus = {{ uncomment_goods_list | safe }};
</script>
<script type="text/javascript" src="{{ static('js/common.js') }}"></script>
<script type="text/javascript" src="{{ static('js/goods_judge.js') }}"></script>
```

##### 添加 logo 图片

> 添加图片:

```html
<img src="{{ static('images/logo.png') }}">
```

##### 更改中间部分视图:

```html
 <div class="judge_con" v-for="(sku,index) in skus">
        <div class="judge_goods fl">
            <ul>
                <li>
                    <a :href="sku.url"><img :src="sku.default_image_url"></a>
                    <h4><a :href="sku.url">[[ sku.name ]]</a></h4>
                    <div class="price">￥[[ sku.price ]]</div>
                </li>
            </ul>
        </div>
        <div class="judge_goods_input fr">
            <div class="judge_item">
                <label>商品满意度</label>
                <div class="stars fl">
                    <a @mouseover="on_stars_mouseover(index, 1)" @mouseout="on_stars_mouseout(index)" @click="on_stars_click(index, 1)" class="star_off" :class="sku.score>=1?'light':''"></a>
					<a @mouseover="on_stars_mouseover(index, 2)" @mouseout="on_stars_mouseout(index)" @click="on_stars_click(index, 2)" class="star_off" :class="sku.score>=2?'light':''"></a>
					<a @mouseover="on_stars_mouseover(index, 3)" @mouseout="on_stars_mouseout(index)" @click="on_stars_click(index, 3)" class="star_off" :class="sku.score>=3?'light':''"></a>
					<a @mouseover="on_stars_mouseover(index, 4)" @mouseout="on_stars_mouseout(index)" @click="on_stars_click(index, 4)" class="star_off" :class="sku.score>=4?'light':''"></a>
					<a @mouseover="on_stars_mouseover(index, 5)" @mouseout="on_stars_mouseout(index)" @click="on_stars_click(index, 5)" class="star_off" :class="sku.score>=5?'light':''"></a>
                </div>
                <div class="score">[[ sku.display_score ]]分</div>
            </div>
            <div class="judge_item">
                <label>发表评价</label>
                <textarea class="judge_area" v-model="sku.comment"></textarea>
            </div>
            <div class="judge_item">
                <input type="input" @click="save_comment(index)" value="提 交" class="judge_sub">
                <input type="checkbox" v-model="sku.is_anonymous" class="no_name"> 匿名评价
            </div>
        </div>
    </div>
```





### 2. 评价订单商品

<img src="/payment/images/16填写评价信息.png" style="zoom:30%">

> **1.请求方式**

| 选项 | 方案 |
| ---------------- | ---------------- |
| **请求方法** | POST |
| **请求地址** | /orders/comment/ |

> **2.请求参数：查询参数**

| 参数名 | 类型 | 是否必传 | 说明 |
| ---------------- | ---------------- | ---------------- | ---------------- |
| **order_id** | int | 是 | 订单编号 |

> **3.响应结果：JSON**

| 字段 | 说明 |
| ---------------- | ---------------- |
| **code** | 状态码 |
| **errmsg** | 错误信息 |

> **4.后端接口定义和实现**

```python
class OrderCommentView(LoginRequiredMixin, View):
    """订单商品评价"""

    def get(self, request):
        """展示商品评价页面"""
        ......

    def post(self, request):
        """评价订单商品"""
        # 接收参数
        json_dict = json.loads(request.body.decode())
        order_id = json_dict.get('order_id')
        sku_id = json_dict.get('sku_id')
        score = json_dict.get('score')
        comment = json_dict.get('comment')
        is_anonymous = json_dict.get('is_anonymous')
        # 校验参数
        if not all([order_id, sku_id, score, comment]):
            return http.HttpResponseForbidden('缺少必传参数')
        try:
            OrderInfo.objects.filter(order_id=order_id, 
                                     user=request.user, 
                       status=OrderInfo.ORDER_STATUS_ENUM['UNCOMMENT'])
        except OrderInfo.DoesNotExist:
            return http.HttpResponseForbidden('参数order_id错误')
        try:
            sku = SKU.objects.get(id=sku_id)
        except SKU.DoesNotExist:
            return http.HttpResponseForbidden('参数sku_id错误')
        if is_anonymous:
            if not isinstance(is_anonymous, bool):
                return http.HttpResponseForbidden('参数is_anonymous错误')

        # 保存订单商品评价数据
        OrderGoods.objects.filter(order_id=order_id, 
                                  sku_id=sku_id, 
                                  is_commented=False).update(
            comment=comment,
            score=score,
            is_anonymous=is_anonymous,
            is_commented=True
        )

        # 累计评论数据
        sku.comments += 1
        sku.save()
        sku.goods.comments += 1
        sku.goods.save()

        # 如果所有订单商品都已评价，则修改订单状态为已完成
        if OrderGoods.objects.filter(order_id=order_id, 
                                     is_commented=False).count() == 0:
            OrderInfo.objects.filter(order_id=order_id).update(status=OrderInfo.ORDER_STATUS_ENUM['FINISHED'])

        return http.JsonResponse({'code': RETCODE.OK, 
                                  'errmsg': '评价成功'})
```

##### 

