#### 项目中间件的使用

laravel内置了一个中间件来验证用户是否经过认证，如果用户没有经过认证，中间件会将用户重定向到登录页面，否则如果用户经过认证，中间件就会允许请求继续往前进入下一步操作。（路由那部分）

#### laravel生命周期

2.1 加载项目依赖

2.2 创建 Laravel 应用实例 

- 2.2.1 创建应用实例
- 2.2.2 内核绑定
- 2.2.3 注册异常处理
- 2.2.4 小结

2.3 接收请求并响应 

- 2.3.1 解析内核实例
- 2.3.2 处理 HTTP 请求 
  - 2.3.2.1 创建请求实例
  - 2.3.2.2 处理请求 
    - 2.3.2.2.1 启动「引导程序」
    - 2.3.2.2.2 发送请求至路由

2.4 发送响应

2.5 终止程序

#### Thinkphp5.0和laravel之间的区别

渲染模板：

Thinkphp：$this->display()

Laravel:return view()

跨站方式：

Thinkphp：需要手动完成防止跨站攻击的代码

Laravel：考虑到跨转请求伪造，使用form表单以post方式传值，且要在form表单中加入{{ csrf_field() }}来完成，不加会显示token令牌错误

路由性：

Thinkphp（3.2）：必要要填写控制器，否则无法访问

Lavarel：是一个重路由的框架，所有的功能都是由路由发起的。他可以没有控制器方法，没有模型，没有视图，但是一定要有路由。

中间件：

Thinkphp：没有中间件

Laravel：具有中间件，可以实现访问前后的处理（请求和返回，权限认证等）

If和foreach语句区别：

Thinkphp：和原生php中的写法一致

Laravel：在编写是一定要添加@符号（@if-@endif @foreach-@endforeach）

加密方式：

Thinkphp：是采用thinkphp内置算法中的md5（）加密算法，易被外界逆向破解，强行破除。

Laravel：采用内置的hash加密（单向加密方法），没有逆向破解的说法