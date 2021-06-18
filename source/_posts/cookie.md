---
title: cookie
tags: javaWeb
category: javaWeb
---


这是关于javaWeb中cookie的学习博客，记录一些cookie的常见用法和细节

## javaWeb重定向
>1、response.sendRedirect("")
>2、

## cookie的细节
>1、cookie保存的是键值对
>2、一个web站点可以给浏览器发送多个cookie，浏览器也可以存储多个cookie
>3、浏览器一般只能存放300个cookie，每个cookie的大小限制为4KB
>4、创建了cookie，并发送到浏览器，默认是会话级别的（存储在内存中），
 设置过期时间会让cookie存储在本地,将过期时间设为0；则是要删除cookie，设置时间的api是setMaxAge.
>5、删除cookie时，路径要相同，否则不会被删除。

## cookie常用api


>1.setMaxAge  设置session有效期，正值为以秒计算过时失效,负值为关闭客户端失效,默认值为-1。
>2、setDomain  用于指定只有请求了指定的域名，才会带上该cookie
>3、setPath  只有访问该域名下的cookieDemo的这个路径地址才会带cookie
>4、setValue  重置value的值


### 实例代码

``` javascript
protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		//获取客户段发过来的cookie
		Cookie[] cookies = request.getCookies();
		if(cookies!=null) {
			for (Cookie c : cookies) {
				System.out.println(c.getName()+"="+c.getValue());
			}
		}
		
		response.getWriter().write("Hello cookie..");
		
		//添加多个cookie
		Cookie cookie = new Cookie("name","刘备");
		//cookie的有效期
		//正值为以秒计算过时失效,负值为关闭客户端失效,默认值为-1
		cookie.setMaxAge(60*60*24*3);  		//设置有效期为3天
		response.addCookie(cookie);
		Cookie cookie2 = new Cookie("age","63");
		response.addCookie(cookie2);
		
		//用于指定只有请求了指定的域名，才会带上该cookie
		cookie.setDomain(".huaban.com");
		
		//只有访问该域名下的cookieDemo的这个路径地址才会带cookie
		cookie.setPath("/CookieDemo");
	}
```

### 结合springboot返回cookie