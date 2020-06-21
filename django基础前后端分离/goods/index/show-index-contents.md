# 展示首页商品广告

### 1. 分析首页商品广告数据结构

<img src="/goods/images/39首页广告.png" style="zoom:50%">

<img src="/goods/images/41广告分类.png" style="zoom:35%">

<img src="/goods/images/42广告内容.png" style="zoom:40%">

> 结论：
* 首页商品广告数据由广告分类和广告内容组成。
* 广告分类带有标识符`key`，可以利用它确定广告展示的位置。
* 确定广告展示的位置后，再查询和渲染出该位置的广告内容。
* 广告的内容还有内部的排序字段，决定了广告内容的展示顺序。

### 2. 查询首页商品广告

```python
class IndexView(View):
    """首页广告"""

    def get(self, request):
        """提供首页广告界面"""
        # 查询商品频道和分类
        ...
        # 广告数据
        dict = {}
        content_categories = ContentCategory.objects.all()
        for cat in content_categories:
            dict[cat.key] = cat.content_set.filter(status=True).order_by('sequence')

        # 渲染模板的上下文
        context = {
            'categories': categories,
            'contents': dict,
        }
        return render(request, 'index.html', context)
```

### 3. 渲染首页商品广告

> **1.轮播图广告**

```html
<ul class="slide">
    {% for content in contents.index_lbt %}
    <li><a href="{{ content.url }}"><img src="{{ content.image.url }}" alt="{{ content.title }}"></a></li>
    {% endfor %}
</ul>
```

> **2.快讯和页头广告**

```html
<div class="news">
    <div class="news_title">
        <h3>快讯</h3>
        <a href="#">更多 &gt;</a>
    </div>
    <ul class="news_list">
        {% for content in contents.index_kx %}
        <li><a href="{{ content.url }}">{{ content.title }}</a></li>
        {% endfor %}
    </ul>
    {% for content in contents.index_ytgg %}
    <a href="{{ content.url }}" class="advs"><img src="{{ content.image.url }}"></a>
    {% endfor %}
</div>
```

> **3.楼层广告（一楼）**

```html
<div class="list_model">
    <div class="list_title clearfix">
        <h3 class="fl" id="model01">1F 手机通讯</h3>
        <div class="subtitle fr">
            <a @mouseenter="f1_tab=1" :class="f1_tab===1?'active':''">时尚新品</a>
            <a @mouseenter="f1_tab=2" :class="f1_tab===2?'active':''">畅想低价</a>
            <a @mouseenter="f1_tab=3" :class="f1_tab===3?'active':''">手机配件</a>
        </div>
    </div>
    <div class="goods_con clearfix">
        <div class="goods_banner fl">
            <img src="{{ contents.index_1f_logo.0.image.url}}">
            <div class="channel">
                {% for content in contents.index_1f_pd %}
                <a href="{{ content.url }}">{{ content.title }}</a>
                {% endfor %}
            </div>
            <div class="key_words">
                {% for content in contents.index_1f_bq %}
                <a href="{{ content.url }}">{{ content.title }}</a>
                {% endfor %}
            </div>

        </div>
        <div class="goods_list_con">
            <ul v-show="f1_tab===1" class="goods_list fl">
                {% for content in contents.index_1f_ssxp %}
                <li>
                    <a href="{{ content.url }}" class="goods_pic"><img src="{{ content.image.url }}"></a>
                    <h4><a href="{{ content.url }}" title="{{ content.title }}">{{ content.title }}</a></h4>
                    <div class="price">{{ content.text }}</div>
                </li>
                {% endfor %}
            </ul>
            <ul v-show="f1_tab===2" class="goods_list fl">
                {% for content in contents.index_1f_cxdj %}
                <li>
                    <a href="{{ content.url }}" class="goods_pic"><img src="{{ content.image.url }}"></a>
                    <h4><a href="{{ content.url }}" title="{{ content.title }}">{{ content.title }}</a></h4>
                    <div class="price">{{ content.text }}</div>
                </li>
                {% endfor %}
            </ul>
            <ul v-show="f1_tab===3" class="goods_list fl">
                {% for content in contents.index_1f_sjpj %}
                <li>
                    <a href="{{ content.url }}" class="goods_pic"><img src="{{ content.image.url }}"></a>
                    <h4><a href="{{ content.url }}" title="{{ content.title }}">{{ content.title }}</a></h4>
                    <div class="price">{{ content.text }}</div>
                </li>
                {% endfor %}
            </ul>
        </div>
    </div>
</div>
```

> **4.楼层广告（二楼）**

```html
<div class="list_model model02">
    <div class="list_title clearfix">
        <h3 class="fl" id="model01">2F 电脑数码</h3>
        <div class="subtitle fr">
            <a @mouseenter="f2_tab=1" :class="f2_tab===1?'active':''">加价换购</a>
            <a @mouseenter="f2_tab=2" :class="f2_tab===2?'active':''">畅享低价</a>
        </div>
    </div>
    <div class="goods_con clearfix">
        <div class="goods_banner fl">
            <img src="{{ contents.index_2f_logo.0.image.url}}">
            <div class="channel">
                {% for content in contents.index_2f_pd %}
                <a href="{{ content.url }}">{{ content.title }}</a>
                {% endfor %}
            </div>
            <div class="key_words">
                {% for content in contents.index_2f_bq %}
                <a href="{{ content.url }}">{{ content.title }}</a>
                {% endfor %}
            </div>
        </div>
        <div class="goods_list_con">
            <ul v-show="f2_tab===1" class="goods_list fl">
                {% for content in contents.index_2f_cxdj %}
                <li>
                    <a href="{{ content.url }}" class="goods_pic"><img src="{{ content.image.url }}"></a>
                    <h4><a href="{{ content.url }}" title="{{ content.title }}">{{ content.title }}</a></h4>
                    <div class="price">{{ content.text }}</div>
                </li>
                {% endfor %}
            </ul>
            <ul v-show="f2_tab===2" class="goods_list fl">
                {% for content in contents.index_2f_jjhg %}
                <li>
                    <a href="{{ content.url }}" class="goods_pic"><img src="{{ content.image.url }}"></a>
                    <h4><a href="{{ content.url }}" title="{{ content.title }}">{{ content.title }}</a></h4>
                    <div class="price">{{ content.text }}</div>
                </li>
                {% endfor %}
            </ul>
        </div>
    </div>
</div>
```

> **5.楼层广告（三楼）**

```html
<div class="list_model model03">
    <div class="list_title clearfix">
        <h3 class="fl" id="model01">3F 家居家装</h3>
        <div class="subtitle fr">
            <a @mouseenter="f3_tab=1" :class="f3_tab===1?'active':''">生活用品</a>
            <a @mouseenter="f3_tab=2" :class="f3_tab===2?'active':''">厨房用品</a>
        </div>
    </div>
    <div class="goods_con clearfix">
        <div class="goods_banner fl">
            <img src="{{ contents.index_3f_logo.0.image.url }}">
            <div class="channel">
                {% for content in contents.index_3f_pd %}
                <a href="{{ content.url }}">{{ content.title }}</a>
                {% endfor %}
            </div>
            <div class="key_words">
                {% for content in contents.index_3f_bq %}
                <a href="{{ content.url }}">{{ content.title }}</a>
                {% endfor %}
            </div>
        </div>
        <div class="goods_list_con">
            <ul v-show="f3_tab===1" class="goods_list fl">
                {% for content in contents.index_3f_shyp %}
                <li>
                    <a href="{{ content.url }}" class="goods_pic"><img src="{{ content.image.url }}"></a>
                    <h4><a href="{{ content.url }}" title="{{ content.title }}">{{ content.title }}</a></h4>
                    <div class="price">{{ content.text }}</div>
                </li>
                {% endfor %}
            </ul>
            <ul v-show="f3_tab===2" class="goods_list fl">
                {% for content in contents.index_3f_cfyp %}
                <li>
                    <a href="{{ content.url }}" class="goods_pic"><img src="{{ content.image.url }}"></a>
                    <h4><a href="{{ content.url }}" title="{{ content.title }}">{{ content.title }}</a></h4>
                    <div class="price">{{ content.text }}</div>
                </li>
                {% endfor %}
            </ul>
        </div>
    </div>
</div>
```