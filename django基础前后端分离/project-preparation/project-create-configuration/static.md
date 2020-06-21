# <font color="orange">配置前端静态文件   </font>

### <font color="blue">配置静态文件目录   </font>

<img src="/project-preparation/images/statictian.png" style="zoom:50%">

### <font color="blue">指定静态文件加载路径   </font>

> 在  dev.py 文件中: 

```python
# 默认就有的部分: 
STATIC_URL = '/static/'

# 添加如下选项: 配置静态文件加载路径
STATICFILES_DIRS = [
    os.path.join(BASE_DIR, 'static')
]
```

> 配置完成后：运行程序，测试结果: 
>
> http://127.0.0.1:8008/static/index.html



### <font color="blue">总结:    </font>

* 在 dev.py 文件中添加 STATICFILES_DIRS 配置
* 使我们能够正确的访问到静态文件夹中的问价.