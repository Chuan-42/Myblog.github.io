---
title: Java过滤器跨域
tags: [api, 跨域]
categories: java
---

# Java 过滤器跨域

### 1、创建拦截过滤器

- 工具：Eclipse

- 项目类型：Java webapi

  右键 >> new >> Filter

### 2、方法介绍

##### ①：**doFilter**()方法中进行过滤操作， 常用代码有获取请求、获取响应、获取 session、放行、根据 session 的属性进行过滤操作、设置编码格式等等

```java
  @Override
	public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
			throws IOException, ServletException {
			// TODO Auto-generated method stub
      // place your code here

      // pass the request along the filter chain
      chain.doFilter(request, response);
	}
```

##### ②：init()方法可以获得初始化参数，一般不用重写

```java
  @Override
	public void init(FilterConfig filterConfig) throws ServletException {
    	// TODO Auto-generated method stub
	}
```

##### ③：destroy()方法一般用重写

```java
  @Override
	public void destroy() {
			// TODO Auto-generated method stub
	}
```

### 3、拦截处理

##### ①：通过判断看是否进行跨域处理

```java
	private boolean isCross = false;
```

##### ②：重写时设置初始值为 false

```java
	@Override
  public void destroy() {
    isCross = false;
  }
```

##### ③：初始化获取 isCross 值，判断是否进行跨域处理

```java
	@Override
	public void init(FilterConfig filterConfig) throws ServletException {
		isCross = filterConfig.getInitParameter("IsCross").equals("true");
	}
```

##### ④：修改 response 请求头进行跨域

```java
  @Override
	public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
			throws IOException, ServletException {
		System.out.println("************************************************");
		HttpServletRequest req = (HttpServletRequest) request;
		HttpServletResponse res = (HttpServletResponse) response;
		if (isCross) {
			res.setHeader("Access-Control-Allow-Origin", "*");
			res.setHeader("Access-Control-Allow-Credentials", "true");
			res.setHeader("Access-Control-Allow-Headers",
					"Accept,Accept-Encoding,Accept-Language,Connection,Content-Length,Content-Type, Cookie,Host,Origin,Referer,User-Agent, Authorization,X-Requested-With,OsType");
			res.setHeader("Access-Control-Allow-Methods", "POST,GET, HEAD,PUT, DELETE, TRACE, OPTIONS");
		}
		chain.doFilter(req, res);
	}
```

### 4、配置 web.xml 文件

```xml
<filter>
  <filter-name>crossFilter</filter-name>
  <filter-class>com.sybinal.shop.filter.CrossFilter</filter-class>
  <init-param>
    <param-name>IsCross</param-name>
    <param-value>true</param-value>
  </init-param>
</filter>
<filter-mapping>
  <filter-name>crossFilter</filter-name>
  <url-pattern>/api/*</url-pattern>
</filter-mapping>
```
