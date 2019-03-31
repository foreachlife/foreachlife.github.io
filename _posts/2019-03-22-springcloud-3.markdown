---
layout:         post
title:     		springcloud(2)-feign
author:     	YY
tag:            springcloud
subtitle:    	
excerpt_separator: ""
dataset:    /projectors/data.json
---
<h1>Ribbon</h1>
<hr>
 
<h3>Feign是一个声明式的伪Http客户端，它使得写Http客户端变得更简单。使用Feign，只需要创建一个接口并注解。它具有可插拔的注解特性，可使用Feign 注解和JAX-RS注解。Feign支持可插拔的编码器和解码器。
Feign默认集成了Ribbon，并和Eureka结合，默认实现了负载均衡的效果。</h3>

<br>
<h2>Feign 采用的是基于接口的注解</h2>
<h2>Feign 整合了ribbon，具有负载均衡的能力</h2>
<h2>整合了Hystrix，具有熔断的能力</h2>
<hr>
<br>
<h2><font color="red">如何配置feign</font></h2>

<h3>创建一个子模块service-fegin,导入依赖spring-cloud-starter-netflix-ribbon，spring-cloud-starter-netflix-eureka-server,spring-cloud-starter-openfeign,继承父模块。</h3>
<h3>然后在启动类中引入@EnableEurekaClient,@EnableDiscoveryClient,@EnableFeignClients</h3>
{% highlight ruby %}
@RestController
@SpringBootApplication
@EnableEurekaClient
@EnableDiscoveryClient
@EnableFeignClients
public class ServiceFeignApplication {

	public static void main(String[] args) {
		SpringApplication.run(ServiceFeignApplication.class, args);
	}

}
{% endhighlight ruby %}


<h3>然后创建两个类helloRest,helloService，如下:</h3>
<h4>helloRest</h4>

{% highlight ruby %}
@RestController
public class HelloRest {

    @Autowired
    HelloService helloService;

    @RequestMapping(value = "/hello")
    public String hello() {
        return helloService.helloService();
    }


}
{% endhighlight ruby %}
<br>
<h4>helloService ,这里@feignClient调用注册的服务名称service-hello,下面的url为服务中的hellourl路径</h4>

{% highlight ruby %}

@RestController
@FeignClient(value = "service-hello")
public interface HelloService {

    @RequestMapping(value = "/hello",method = RequestMethod.GET)
    String helloFromClientOne();
}
{% endhighlight ruby %}

<h3>然后启动服务,调用service-feign的端口对应的服务,feign就会调用服务中注册的hello服务</h3>















