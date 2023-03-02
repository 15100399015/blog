所谓第三方登录，实质就是OAuth授权，用户想要去登录A网站，A网站让用户提供第三方网站的数据，证明自己的身份，获取第三方网站的身份数据，就需要OAuth授权

举例来说，A网站需要允许GitHub登录，背后的逻辑是下面的流程
- A网站让用户跳转到GitHUb
- GitHub要求用户登录，然后询问A网站要求获得XX权限，你是否同意
- 用户同意，GitHub就会重定向回A网站，同时发送一个授权码
- A网站使用授权码，向GitHub请求令牌
- GitHub 返回令牌
- A 网站使用令牌，向GitHub请求用户数据

应用登记

一个应用要求OAuth授权，必须先到对方网站登记，让对方知道是谁在请求

所以你要先去GitHub登记一下，当然，我已经登记过了，你使用我登记的信息也可以，但为了走


浏览器跳转GitHub

示例很简单，就是一个链接，让用户跳转到GitHub

跳转的URL如下


https://github.com/login/oauth/authorize?
  client_id=7e015d8ce32370079895&
  redirect_uri=http://localhost:8080/oauth/redirect


这个URL指向了GitHub的OAuth授权网址，带有两个参数Client_id告诉Github谁在请求，redirect_url是稍后跳转回来的网址

用户点击到GitHub,GitHub会要求用户登录，确保本人在操作


授权码

登录后，GitHub询问用户，该用户正在请求数据，你是否统一授权，

用户同意授权，GtiHub 就会跳转到redirect_uri指定的跳转的网址，并且带上授权码，跳转回来的url就是下面的样子
后端收到请求后就收到了授权码