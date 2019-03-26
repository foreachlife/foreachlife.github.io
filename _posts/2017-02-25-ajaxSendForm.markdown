---
layout:     	post
title:     		AJax提交表单数据到后台springmvc接收
author:     	YY
tags:           ajax
subtitle:    	
excerpt_separator: ""
dataset:    /projectors/data.json
---

<h4>第一种方法直接用serialize()方法</h4>
{% highlight ruby %}
function insert(){
        $.ajax({
            type:"POST",
            url:"${pageContext.request.contextPath}/order/insert",
            data : $("#fom").serialize(),             
            success  :function (res) {
                console.log(res); 
            error:function () {                
            }
        });
}

{% endhighlight ruby %}

<h4>后台springmvc用对象参数接收 可以自动转换为对象,需要注意的就是form表单中的name要和对象中的参数名相同</h4>
{% highlight ruby %}
@RequestMapping(value = "/insert",method = RequestMethod.POST)
        public String insert( Order order){
           int result=this.orderSerivce.insert(order);
           if(result==1){
               System.out.println("添加失败");
               return "101";
           }
           return "100";
        }


{% endhighlight ruby %}

<h4>第二种是用JSON.stringify()将json对象转化为json对象的字符串传递</h4>


{% highlight ruby %}
function insert(){
        $.ajax({
            type:"POST",
            url:"${pageContext.request.contextPath}/user/insert",
            async:false,
            data :JSON.stringify({
                username : $("input[name='username']").val(),
                password: $("input[name='password']").val(),
                role : {
                    id : "",
                    name: $("select[name='name']").val()
                }
            }),
            contentType: "application/json;charset=UTF-8",
          /*如果不写这个,仔细看后台会出现Content type 'application/x-www-form-urlencoded;charset=UTF-8' not supported   */ 
            dataType:"json",
            success  :function (res) {
                console.log(res);
                if(res==100){
                    $("#msg").html("<font size='60px'> success </font>").show(700).delay(3000).hide(500);
                }else {
                    $("#msg").html("<font size='60px'>  fail </font>").show(700).delay(3000).hide(500);
                }
                window.location.href="http://localhost:8080/user/findall?page=1"
            },
            error:function () {
                $("#msg").html("<font size='60px'>  fail </font>").show(700).delay(3000).hide(500);
                window.location.href="http://localhost:8080/user/findall?page=1"
            }
        });
    }

{% endhighlight ruby %}


<h4>后台用@RequestBody接收, @RequestBody只接收JSON对象的字符串</h4>


{% highlight ruby %}
@ResponseBody
@RequestMapping(value = "/insert",method = RequestMethod.POST)
public String insert(@RequestBody User user){
   int result=this.userSerivce.insert(user);
   if(result==0){
       return "101";
   }
   return "100";
}
{% endhighlight ruby %}
