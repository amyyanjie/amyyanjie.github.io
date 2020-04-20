---
title: SpringMVC技巧
english_title: SpringMVC-technique
date: 2018-01-25 12:10:40
tags: SpringMVC
categories: Spring
---
## 注解映射技巧
@RequestMapping注解：

(1)支持标准的URL，   
(2)支持Ant风格的URL（?表示匹配一个字符，*表示匹配任一字符，**表示匹配任一的URL的路径）    
(3)占{xxx}占位符的URL。占位符可以通过类似于参数的方式，提前占到一个URL映射。后面可以用对应的用户提交的具体的值来替换掉对应的占位符。如/user/{userId}匹配user/123、user/abc等URL，123和abc可以在方法中以参数的方式传入，对应的{userId}。

## 请求方法细节处理
1.请求参数绑定   
2.请求方式限制   
3.请求转发和重定向   
4.数据模型赋值   
5.返回json数据   
6.cookie访问

##### 例：1、2、3、4点体现如下
```
    @RequestMapping(value = "/{seckillId}/detail",method = RequestMethod.GET)
    
    //@RequestMapping映射URL，绑定到方法上。
    
//seckillId通过注解@PathVariable()绑定方法的参数，对应URL的占位符。
//当用户传入对应URL时,它会把URL中的seckillId部分获取到传入到detail方法中。

//method = RequestMethod.GET  这个请求只允许GET方法访问，若是POST方法访问，SpringMVC将不作映射

    public String detail(@PathVariable("seckillId") Long seckillId, Model model)
    //Model model，用来承载最终要返回给用户的数据。
    
    {
        if (seckillId == null)
        {
            return "redirect:/seckill/list";
            //请求的重定向
        }
       Seckill seckill=seckillService.getById(seckillId);
        if (seckill==null)
        {
            return "forward:/seckill/list";
            //请求的转发
        }
        
        model.addAttribute("seckill",seckill);
        //传入seckill，通过key/value形式映射，在页面中可以通过seckill去拿到对应的model中的数据。
       
        return "detail";
        //return "detail"字符串，根据配置，默认的字符串是返回的对应的jsp页面。即detail.jsp。会把model数据传给detail.jsp做相应的数据的替换，最终输出这张jsp的html。
    }

```
##### 返回json数据
```
    @RequestMapping(value = "/{seckillId}/{md5}/execution",
            method = RequestMethod.POST,
            produces = {"application/json;charset=UTF-8"})
            //在RequestMapping传入produces，用来返回httpResponse的tagger，告诉浏览器这是一个application/json    
        
    @ResponseBody
    //告诉返回数据类型是json
    
    public SeckillResult<SeckillExecution> execute(@PathVariable("seckillId") Long seckillId,
                                                   @PathVariable("md5") String md5,
                                                   @CookieValue(value = "userPhone",required = false) Long userPhone)
  {
    SeckilResult<Exposer>result;
    //SeckilResult对象会把里面的数据封装成json，返回给浏览器
    
    try{
        Exposer exposer=·····
    }catch(Exception e){
        logger.error(e.getMessage(),e);
        result=new SeckillResult<Exposer>(false,e.getMessage());
    }
    return result;
  }
```
##### Cookie访问
 ```
 @CookieValue(value = "userPhone",required = false) Long userPhone)
 //required = false表示不强制传入对应的cookie。当没有对应的userPhone这个cookie时，不做相应拦截。也进入到这个方法。
 ```
 ## 前后端交互思考
 
 ##### $.get()，$.post()
 1. ajax异步请求.JavaScript发送AJAX请求,URL域名和当前页面一致。

2. Controller接收用户请求，@RequestMapping映射URL，请求参数绑定到方法上。Controller内依赖注入Service，调用service方法处理数据，注意其返回类型，得到数据结果。

VO用来封装所有的数据结果，可以新建dto类（如SeckillResult），为所有的ajax请求返回类型，全部封装成json数据。定义变量及所需的不同构造函数（参数有data，即是sevice处理得到的数据结果）。

result=new SeckillResult<service方法返回类>（构造函数参数） 。泛型。最后return result;

3. 在js中通过ajax拿到data。
$.get()，$.post():使用AJAX的HTTP GET或POST请求从服务器加载数据。

```
$.get(seckill.URL.now(), {}, function (result) {
                if (result && result['success']) {
                    var nowTime = result['data'];
                    //时间判断,计时交互
                    countdown(seckillId, nowTime, startTime, endTime)

                } else {
                    console.log('result:' + result);
                }
            });
```

回调函数function(result){})是一种以参数形式传递给另一个函数的函数。拿到result之后。通过result[data]可以取得相应数据。
 
 ##### $.ajax()
 
 ```
 getAllAccusedNames:function(){
        $.ajax({
            async :true,
            method:'get',
            dataType:'json',
            data:{processId:commonReport.processId},
            url:Global.rootPath+"/verdictReport/getTlParseAccusedList",

            success:function (parseAccusedList){
                //回调函数内代码
            }
        })
    }
 ```
 
 - 可先将$.ajax()进行封装，成一个函数。在js中用到ajax()时，直接调动.getByAjax(参数)。参数则是对应的settings。
 
 ```
    getByAjax: function (async, methodType, returnType, url, data, 
    callbackMethod_success, callbackMethod_false) {
            $.ajax({
                async: async,
                type: methodType,
                dataType: returnType,
                url: Global.rootPath + url,
                data: data,
                success: function (data) {
                    callbackMethod_success(data);
                },
                error: function (xhr) {
                    //可在此处新增权限提示，或者超时重登录提示
                    callbackMethod_false();
                }
            });
        }
 ```
调用时：
```
getAllAccusedNames: function () {
        common.ajax.getByAjax(true, "get", "json",
        "/verdictReport/getTlParseAccusedList",
            {
                processId: commonReport.processId
            },
            function (parseAccusedList) {
                //代码
            },
            function () {
            })
    }
```