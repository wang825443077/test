# 评价订单商品

> 提示：

> 点击《我的订单》页面中的《待评价》按钮，进入到订单商品评价页面。

在 用户中心页面添加如下代码:

```python
<div class="user_link fl">
    <span>|</span>
    <a href="{{ url('users:info') }}">用户中心</a>
    <span>|</span>
    <a href="{{ url('carts:info') }}">我的购物车</a>
    <span>|</span>
    <a href="{{ url('orders:info', args=(1, )) }}">· 我的订单</a>
    <span>|</span>
    <a href="{{ url('users:pass') }}">· 修改密码</a>
</div>
```



