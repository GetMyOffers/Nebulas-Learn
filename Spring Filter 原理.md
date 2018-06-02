*Note: Directly copied from [Spring Boot实战之Filter实现简单的Http Basic认证](https://blog.csdn.net/sun_t89/article/details/51916834)*

### 1、Filter 功能

它使用户可以改变一个 request 和修改一个 response. Filter 不是一个 servlet，它不能产生一个 response，它能够在一个 request 到达 servlet 之前预处理 request，也可以在离开 servlet时处理 response.换种说法，filter 其实是一个 ”servlet chaining” (servlet 链).
一个Filter包括：
1）在servlet被调用之前截获;
2）在servlet被调用之前检查servlet request;
3）根据需要修改request头和request数据;
4）根据需要修改response头和response数据;
5）在servlet被调用之后截获.

### 2、定义自己的过滤器

新增HTTPBasicAuthorizeAttribute.java

如果请求的Header中存在Authorization: Basic 头信息，且用户名密码正确，则继续原来的请求，否则返回没有权限的错误信息

```java
package com.xiaofangtech.sunt.filter;

import java.io.IOException;

import javax.servlet.Filter;
import javax.servlet.FilterChain;
import javax.servlet.FilterConfig;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.fasterxml.jackson.databind.ObjectMapper;
import com.xiaofangtech.sunt.utils.ResultMsg;
import com.xiaofangtech.sunt.utils.ResultStatusCode;
import sun.misc.BASE64Decoder;

@SuppressWarnings("restriction")
public class HTTPBasicAuthorizeAttribute implements Filter{
	
	private static String Name = "test";
	private static String Password = "test";

	@Override
	public void destroy() {
		// TODO Auto-generated method stub
		
	}

	@Override
	public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
			throws IOException, ServletException {
		// TODO Auto-generated method stub
		
		ResultStatusCode resultStatusCode = checkHTTPBasicAuthorize(request);
		if (resultStatusCode != ResultStatusCode.OK)
		{
			HttpServletResponse httpResponse = (HttpServletResponse) response;
			httpResponse.setCharacterEncoding("UTF-8");  
			httpResponse.setContentType("application/json; charset=utf-8"); 
			httpResponse.setStatus(HttpServletResponse.SC_UNAUTHORIZED);

			ObjectMapper mapper = new ObjectMapper();
			
			ResultMsg resultMsg = new ResultMsg(ResultStatusCode.PERMISSION_DENIED.getErrcode(), ResultStatusCode.PERMISSION_DENIED.getErrmsg(), null);
			httpResponse.getWriter().write(mapper.writeValueAsString(resultMsg));
			return;
		}
		else
		{
			chain.doFilter(request, response);
		}
	}

	@Override
	public void init(FilterConfig arg0) throws ServletException {
		// TODO Auto-generated method stub
		
	}
	
	private ResultStatusCode checkHTTPBasicAuthorize(ServletRequest request)
	{
		try
		{
			HttpServletRequest httpRequest = (HttpServletRequest)request;
			String auth = httpRequest.getHeader("Authorization");
			if ((auth != null) && (auth.length() > 6))
			{
				String HeadStr = auth.substring(0, 5).toLowerCase();
				if (HeadStr.compareTo("basic") == 0)
				{
					auth = auth.substring(6, auth.length());  
		            String decodedAuth = getFromBASE64(auth);
		            if (decodedAuth != null)
		            {
		            	String[] UserArray = decodedAuth.split(":");
		            	
		            	if (UserArray != null && UserArray.length == 2)
		            	{
		            		if (UserArray[0].compareTo(Name) == 0
			            			&& UserArray[1].compareTo(Password) == 0)
		            		{
		            			return ResultStatusCode.OK;
		            		}
		            	}
		            }
				}
			}
			return ResultStatusCode.PERMISSION_DENIED;
		}
		catch(Exception ex)
		{
			return ResultStatusCode.PERMISSION_DENIED;
		}
		
	}
	
	private String getFromBASE64(String s) {  
        if (s == null)  
            return null;  
        BASE64Decoder decoder = new BASE64Decoder();  
        try {  
            byte[] b = decoder.decodeBuffer(s);  
            return new String(b);  
        } catch (Exception e) {  
            return null;  
        }  
    }

}

```

### 3、在SpringRestApplication类中注册过滤器，给user/*都加上http basic认证过滤器

```java
package com.xiaofangtech.sunt;

import java.util.ArrayList;
import java.util.List;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.context.embedded.FilterRegistrationBean;
import org.springframework.context.annotation.Bean;

import com.xiaofangtech.sunt.filter.HTTPBasicAuthorizeAttribute;

@SpringBootApplication
public class SpringRestApplication {

	public static void main(String[] args) {
		SpringApplication.run(SpringRestApplication.class, args);
	}
	
	@Bean
    public FilterRegistrationBean  filterRegistrationBean() {
		FilterRegistrationBean registrationBean = new FilterRegistrationBean();
		HTTPBasicAuthorizeAttribute httpBasicFilter = new HTTPBasicAuthorizeAttribute();
		registrationBean.setFilter(httpBasicFilter);
		List<String> urlPatterns = new ArrayList<String>();
	    urlPatterns.add("/user/*");
	    registrationBean.setUrlPatterns(urlPatterns);
	    return registrationBean;
    }
}

```

