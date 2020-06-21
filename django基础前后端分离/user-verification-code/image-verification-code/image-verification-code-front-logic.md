# <font color="orange">图形验证码前端逻辑   </font>

### <font color="blue">Vue 实现图形验证码展示   </font>

##### register.js 文件中实现的逻辑代码: 

```js
mounted( ){
    // 生成图形验证码
    this.generate_image_code();
},
methods: {
    // 生成图形验证码
    generate_image_code(){
        // 生成UUID 
        // generateUUID() : 封装在 common.js 文件中，需要提前引入
        this.uuid = generateUUID();
        // 拼接图形验证码请求地址
        this.image_code_url = "/image_codes/" + this.uuid + "/";
    },
    ......
}
```

> ****

##### 导入注意: 

> 上面的代码中用到的 generateUUID( ) 函数是由这个 comment.js 文件提供的.
>
> 所以在 register.html 中, 导入 register.js 之前需要先导入该文件, 如图所示: 

```html
    <!--该文件导入的时候, 一定要在register.js前面-->
    <script type="text/javascript" src="js/common.js"></script>
    <script type="text/javascript" src="js/register.js"></script>
```



### <font color="blue">Vue 实现图形验证码校验   </font>

##### register.html 中代码: 

```html
<li>
    <label>图形验证码:</label>
    <input type="text" v-model="image_code" @blur="check_image_code" name="image_code" id="pic_code" class="msg_input">
    <img :src="image_code_url" @click="generate_image_code" alt="图形验证码" class="pic_code">
    <span v-show="error_image_code" class="error_tip">{{ error_image_code_message }}</span>
</li>
```

##### register.js 中的逻辑判断

```js
check_image_code(){
    if(!this.image_code) {
        this.error_image_code_message = '请填写图片验证码';
        this.error_image_code = true;
    } else {
        this.error_image_code = false;
    }
},
```

##### 图形验证码校验效果

<img src="/user-verification-code/images/07图形验证码校验效果.png" style="zoom:50%">

