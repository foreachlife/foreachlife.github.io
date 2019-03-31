---
layout:         post
title:     		springcloud(5)-zuul断路器和过滤器实现
author:     	YY
tag:            springcloud
subtitle:    	
excerpt_separator: ""
dataset:    /projectors/data.json
---
<h1>zuul</h1>
<hr>
<h2>Zuul支持断路器Hystrix </h2>

<h3>通过继承ZuulFallbackProvider来实现,getRoute()方法中返回的api-a对应yml中配置的服务名</h3>
 
{% highlight ruby %}
@Component
public class MyZuulFallBack implements FallbackProvider {

    @Override
    public String getRoute() {
        return null;
    }

    @Override
    public ClientHttpResponse fallbackResponse(String route, Throwable cause) {
        return new ClientHttpResponse() {
            @Override
            public HttpStatus getStatusCode() throws IOException {
                return HttpStatus.OK;
            }

            @Override
            public int getRawStatusCode() throws IOException {
                return 200;
            }

            @Override
            public String getStatusText() throws IOException {
                return "OK";
            }

            @Override
            public void close() {

            }

            @Override
            public InputStream getBody() throws IOException {
                return new ByteArrayInputStream("服务已关闭".getBytes());
            }

            @Override
            public HttpHeaders getHeaders() {
                HttpHeaders headers = new HttpHeaders();
                headers.setContentType(MediaType.APPLICATION_JSON);
                return headers;
            }
        };
    }
}
{% endhighlight ruby %}
<h3>将服务service-ribbon关闭再访问,发现不会报错,而是返回配置的错误信息  "服务已关闭"</h3>
<br>


<h2>Zuul配置过滤器</h2>
<hr>

<h3>创建过滤器,写入如下配置</h3>

 
{% highlight ruby %}
@Component
public class MyZuulFilter extends ZuulFilter {

        private static Logger log = LoggerFactory.getLogger(MyZuulFilter.class);
        @Override
        public String filterType() {
            return "pre";
        }

        @Override
        public int filterOrder() {
            return 0;
        }

        @Override
        public boolean shouldFilter() {
            return true;
        }

        @Override
        public Object run() {
            RequestContext ctx = RequestContext.getCurrentContext();
            HttpServletRequest request = ctx.getRequest();
            log.info(String.format("%s >>> %s", request.getMethod(), request.getRequestURL().toString()));
            Object accessToken = request.getParameter("token");
            if(accessToken == null) {
                log.warn("token is empty");
                ctx.setSendZuulResponse(false);
                ctx.setResponseStatusCode(401);
                try {
                    ctx.getResponse().getWriter().write("token is empty");
                }catch (Exception e){}

                return null;
            }
            log.info("ok");
            return null;
        }
{% endhighlight ruby %}
 
<br>

<h3>可以在run方法中做一些token验证,参数验证等操作来过滤请求等</font></h3>
 
     











