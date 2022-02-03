---
title:      ".net5构建RESTFul API"
categories: .NET
date:       2022-01-17 22:13:00
author:     "WenzhongXu"
tags:
    - C#
---

<!-- more -->
根据杨旭老师的[ASP.NET Core 3.x 构建 RESTful API](https://www.bilibili.com/video/BV1XJ411q7yy)学习笔记

### REST
REST即Representational State Transfer（状态表述转换）。描述了Web应用到底是怎样的设计才算是优良的。定义了如下三点：
- 一组网页的网络（一个虚拟状态机）
- 在这些网页上，用户可以通过点击链接来前进（状态转换）
- 点击链接的结果就是下一个网页（表示程序的下一个状态）被传输到用户那里，并渲染好给用户使用

REST风格的API是一套约束规范，是一种架构风格。需要使用一些规范、协议或标准来实现。

### REST的优点
- 性能
- 组件交互的可扩展性
- 组件的可修改性
- 可移植性
- 可靠性
- 可视性

### REST的约束
- 客户端-服务器
- 无状态
- 统一的资源接口/界面
- 多层系统
- 可缓存
- 按需编码（可选）


### 对外合约
#### 资源命名
1. 使用名词而非动词
比如接口是为了获得系统里所有的用户，常见错误的做法是 api/getusers。
正确的做法是 GET api/users。

2. 人类能读懂
命名上要足够友好，并且比较简短。

3. 要体现资源的结构/关系
- 假设存在若干种资源，而用户这个资源与其他资源没有直接关系，那么获取用户的url应该是 api/users，而不是api/product/users或者api/files/product/users，因为他们没有直接的结构关系
- 通过id获取单个用户应该是api/users/{userId}，而不是api/userid/users
- 目的是让api具有很好的预测性和一致性

    1. 案例1 获取某个公司下所有员工信息
    - 分析：使用HTTP的GET，url需要体现公司和员工的包含关系
    - 常见错误做法：api/employees,api/employees/{companyId}
    - 建议做法：GET api/companies/{companyId}/employees

    2. 案例2 获取某个公司的某个员工信息
    - 分析：还是需要体现包含关系
    - 常见错误做法：api/companies/{employeeId},api/employees/{employeeId}
    - 建议做法：GET api/companies/{companyId}/employees/{employeeId}

4. 自定义查询的命名
- 假设需要获取所有用户信息，并要求按照年龄从小到大排列
- 常见错误做法： api/users/orderby/age
- 建议做法： GET api/users?orderby=name

5. 例外
有一些需求总是无法满足RESTful的约束，比如获取系统中用户的数量，只能妥协的做法如下：
GET api/users/totalamounttouser

### HTTP方法
|HTTP方法|请求参数(Payload)|参数位置|URI|请求前|请求后|响应内容|
:-:|:-|-:|:-|-:|-:|:-
|GET|查询参数|可含查询字符串(Query String)|/api/companies/{companyId}  /api/companies|无修改|无修改|单个资源 多个资源的集合|  
|POST|要创建的单个资源|Body|/api/companies|null|{a:1}|新创建的单个资源|
|PATCH|待修改资源的jsonPathDocument|Body|/api/companies/{companyId}|{a:1,b:2}|{a:1,b:3}|无需返回|
|PUT|要替换的单个资源信息|Body|/api/companies/{companyId}|{a:1,b:2}|{a:2,b:3}|无需返回|
|PUT|要创建的单个资源|Body|/api/companies/{companyId}|null|{a:2,b:3}|返回新创建的资源|
|DELETE|无|可含查询字符串(Query String)|/api/companies/{companyId}|{a:1},{b:2}|{a:1}|无需返回|
|HEAD|查询参数|可含查询字符串(Query string)|api/companies/{companyId}  /api/companies|无修改|无修改|不返回响应的Body，用来在资源上获取一些信息|  

### 状态码
#### 1xx
- 属于信息性的状态码，Web API并不使用1xx的状态码

#### 2xx
- 意味着请求执行的很成功
    - 200-Ok，表示请求很成功
    - 201-Created，请求成功并创建了资源
    - 204-No Content，请求成功，但不应该返回任何东西，例如删除操作

#### 3xx
- 用于跳转。例如告诉搜索引擎，某个页面的网址已经永久的改变了。绝大多数的Web API都不需要用这类状态码

#### 4xx
- 客户端错误
    - 400-Bad Request，表示API消费者发送到服务器的请求是有错误的
    - 401-Unauthorized，表示没有提供授权信息或者提供的授权信息不正确
    - 403-Forbidden，表示身份认证已经成功，但是已认证的用户却无法访问请求的资源
    - 404-Not Found，表示请求的资源不存在
    - 405-Method not allowed，当尝试发送请求到资源的时候，使用了不被支持的HTTP方法时，就返回405
    - 406-Not acceptable，这表示API消费者请求的表述格式并不被Web API所支持，并且API不会提供默认的表述格式
    - 409-Conflict，表示请求与服务器当前状态冲突。通常指更新资源时发生的冲突，例如，当你编辑某个资源的时候，该资源在服务器上又进行了更新，所以你编辑的资源版本和服务器的不一致。当然有时候也用来表示你想要创建的资源在服务器上已经存在了。它就是用来处理并发问题的状态码
    - 415- Unsupported media type，与406正好相反，有一些请求必须带着数据发往服务器，这些数据都属于特定的媒体类型，如果API不支持该媒体类型格式，415就会被返回
    - 422 - Unprocessable entity，它是HTTP扩展协议的一部分。它说明服务器已经懂得了实体的Content Type，也就是说415状态码肯定不合适；此外，实体的语法也没有问题，所以400也不合适。但是服务器仍然无法处理这个实体数据，这时就可以返回422。所以它通常是用来表示语意上有错误，通常就表示实体验证的错误

#### 5xx
- 服务器错误
    - 500-Internal server error，表示服务器出现了错误，可能是API的代码有问题

### 错误 Errors
- 错误通常是由API的消费者引起的。API消费者请求时传递的数据是不合理的，这时API就会正常的将其拒绝。
- HTTP 4xx错误
- 并不会影响API的可用性

### 故障 Faults
- 故障是指针对一个合理的请求，API无法返回它的响应，即是API引起的问题。
- HTTP 5xx错误
- 会对API整体的可用性造成影响

### 内容协商
针对一个响应，当有多种表述格式可用的时候，选取一个最佳的表述

##### Accept Header
- Media Type（媒体类型）
    - application/json
    - application/xml
    - ...
- 406 Not Acceptable
- 输出格式
- ASP.NET5里面对应的就是Output Formatters

##### Content-Type Header
- Media Type（媒体类型）
    - application/json
    - application/xml
    - ...
- 输入格式
- ASP.NET5里面对应的就是Input Formatters


### Entity Model和面向外部的Model
#### Entity Model
Entity Framework Core 使用的 Entity Model 是用来表示数据库里面的记录的

#### 面向外部的Model
表示要传输的东西，这类model有时候叫Dto，有时候又叫ViewModel

#### 对比
```C#
public class Person
{
    public Guid Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public DateTimeOffset DateOfBirth { get; set; }
}

public class PersonDto
{
    public Guid Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }
    public decimal Salary { get; set; }
}
```

#### 两者应该分开
为了程序更加健壮、可靠和更易于进化

### 过滤和搜索
#### 如何给API传递数据
- 数据可以通过各种方式来传给API
- Binding source Attributes 会告诉 Model 的绑定引擎从哪里找到绑定源
    - [FromBody],请求的Body
    - [FromForm],请求的Body中的form数据
    - [FromHeader],请求的Header
    - [FromQuery],Query string参数
    - [FromRoute],当前请求中的路由数据
    - [FromService],作为Action参数而注入的服务

例如：
```C#
public async Task<ActionResult<CompanyDto>> GetCompany([FromRoute]Guid companyId)

public async Task<ActionResult<CompanyDto>> GetCompany([FromQuery]Guid companyId)
```

#### [ApiController]
- 默认情况下 ASP .NET Core 会使用 Complex Object Model Binder，它会把数据从Value Providers那里提取出来，而Value Providers的顺序是定义好的。
- 但是我们构建API时通常会使用 [ApiController] 这个属性，为了更好的适应API它改变了上面的规则。
    - [FromBody],通常用来推断复杂类型参数的
    - [FromForm],通常用来推断IFormFile和IFormFileCollection类型的Action参数
    - [FromRoute],用来推断Action的参数名和路由模板中的参数名一致的情况
    - [FromQuery],用来推断其他的Action参数

#### 过滤
- 过滤集合的意思是根据指定条件限定返回的集合
- 例如返回所有类型为国有企业的欧洲公司。URI为： GET /api/companies?type=State-owned&region=Europe
- 所以过滤就是指：我们把某个字段的名字以及想要让该字段匹配的值一起传递给API，并将这些作为返回的集合的一部分

#### 搜索
- 针对集合进行搜索是指根据预定义的一些规则，把符合条件的数据添加到集合里面
- 搜索实际上超出了过滤的范围。针对搜索，通常不会把要匹配的字段名传递过去，通常会把要搜索的值传递给API，然后API自行决定应该对哪些字段来查找该值。经常会是全文搜索。
- 例如： GET /api/companies?q=xxx

#### 比较
过滤：首先是一个完整集合，然后根据条件把匹配或不匹配的数据项移除
搜索：首先是一个空集合，然后根据条件把匹配或不匹配的数据项往里面添加。

#### 注意
- 过滤和搜索这些参数并不是资源的一部分
- 只允许针对资源的字段进行过滤

### 安全性和幂等性
- 安全性是指方法执行后并不会改变资源的表述
- 幂等性是指方法无论执行多少次都会得到同样的结果

|HTTP方法|安全？|幂等？|
:-:|:-:|:-:
|GET|是|是|
|OPTIONS|是|是|
|HEAD|是|是|
|POST|否|否|
|DELETE|否|是|
