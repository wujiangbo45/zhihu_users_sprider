# zhihu_users_sprider
> 入门级的知乎用户爬虫，爬取用户公开的信息以及用户的 following 和 follower 信息。实现了一个单线程登录版本和一个多线程的非登录版，非登录版是直接从浏览器中拷贝了登录知乎后的 cookies 信息

## 总体思路：
- 使用 `广度优先算法` ，分析用户时，将此用户的 following 和 follower 放入 redis 缓存
- 装配好 headers，模拟浏览器请求
- 使用 session 请求
- 通过分析浏览器开发者工具，构造请求及请求参数，所有的请求都是 ajax 请求
- 使用 redis 作为缓存，完成添加用户时的去重，解析后存入 mysql 数据库

## 实现要点
- 登录除了需要用户名、密码、验证码，还有一个表单隐藏域中的 _xsrf 值
- 获取验证码图片时，需要一个 13 位的时间戳，`time.time()` 返回的浮点秒数整数部分是10位，因此使用 `str(int(time.time() * 1000))`
- 本程序没有验证码自动识别，会将验证码下载下来，需要手动输入
- ajax 请求 following 和 follower 后返回的是 json 格式的数据，里面包含了下一页数据的 url 和是否为最后一页的判断，解析即可
- 每个用户都有一个唯一的 `url_token`，因此放入 redis 即可实现去重

## 一些截图

![1](https://github.com/creatorYC/zhihu_users_sprider/blob/master/c8.PNG)
![2](https://github.com/creatorYC/zhihu_users_sprider/blob/master/c9.PNG)
![3](https://github.com/creatorYC/zhihu_users_sprider/blob/master/c10.PNG)
![4](https://github.com/creatorYC/zhihu_users_sprider/blob/master/c11.PNG)

### 注意
如果需要对最终的数据进行分析而又不需要完整的用户信息，可以注释掉获取 follower 的函数，因为这里面获取的用户很多都是三无用户
