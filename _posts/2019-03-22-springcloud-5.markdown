---
layout:         post
title:     		springcloud(5)-网关路由zuul
author:     	YY
tag:            springcloud
subtitle:    	
excerpt_separator: ""
dataset:    /projectors/data.json
---
<h1>zuul</h1>
<hr>
<h2>Zuul是一种网关服务，可提供动态路由，监控，弹性，安全性等。</h2>

<h3>Zuul使用一系列不同类型的过滤器，使我们能够快速灵活地将功能应用于我们的边缘服务。这些过滤器可帮助我们执行以下功能：</h3>
<h3>1 身份验证和安全性 - 确定每个资源的身份验证要求并拒绝不满足这些要求的请求。</h3>
<h3>2 动态路由 - 根据需要动态地将请求路由到不同的后端群集。</h3>
<h3>3 动态路由 - 压力测试 - 逐渐增加群集的流量以衡量性能。</h3>
<h3>4 为每种类型的请求分配容量并删除超过限制的请求。</h3>
<h3>5 静态响应处理 - 直接在边缘构建一些响应，而不是将它们转发到内部集群</h3>
<h3>6 多区域弹性 - 跨AWS区域路由请求，以使我们的ELB使用多样化，并使我们的优势更接近我们的成员</h3>
<br>
<br>

<h2>Zuul主要实现对外请求的负载均衡,将它们转发到不同的服务上去,ribbon是对服务集群的负载均衡</h2>
<hr>

<h3>一 &ensp;&ensp;配置Zuul,新建一个项目名为service-Zuul </h3>
<h3>二 &ensp;&ensp;依赖导入, 因为zuul需要做为一个服务注册,所以要导入spring-cloud-starter-netflix-eureka-client</h3>
<h3>&ensp;&ensp;&ensp;&ensp;&ensp;导入Zuul依赖spring-cloud-starter-netflix-zuul.</h3>
<h3>三 &ensp;&ensp;修改启动类,zuul需要做为服务注册以及调用其它服务</h3>
 
{% highlight ruby %}
@SpringBootApplication
@EnableZuulProxy
@EnableEurekaClient
@EnableDiscoveryClient
public class ServiceZuulApplication {

	public static void main(String[] args) {
		SpringApplication.run(ServiceZuulApplication.class, args);
	}

}

{% endhighlight ruby %}
 
<br>
<h3>四 &ensp;&ensp;修改配置文件application.yml  path为映射路径,serviceId对应具体的服务名称,这里就是根据不同的url前缀转发到不同的服务上</h3>
{% highlight ruby %}
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8080/eureka/
server:
  port: 8777
spring:
  application:
    name: service-zuul
zuul:
  routes:
    api-a:
      path: /api-a/**
      serviceId: service-ribbon
    api-b:
      path: /api-b/**
      serviceId: service-feign  
{% endhighlight ruby %}
<h3>最后启动服务,然后调用,测试一下,发现成功访问了两个服务</font></h3>
 
     











