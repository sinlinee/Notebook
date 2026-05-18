# SpringBoot 笔记

## 1. SpringBoot 基础代码

```
package org.study.study;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class StudyController {

    @GetMapping("/hello")
    public String hello()
    {
        System.out.print("hello");
        return "hello";
    }

}
```
注解：@RestController 作用：将类标记为控制器，并使用HTTP响应返回数据
注解：@GetMapping 作用：将HTTP GET请求映射到hello方法上,并返回字符串"hello"，括号中填路由地址
     包括@PostMapping、@PutMapping、@DeleteMapping等，可以理解为：Flask的@app.route(""Methods=GET)

## 2. 控制器

控制器是应用程序中处理HTTP请求的组件。
在Spring Boot中，提供了@Contollerer和@RestController两个注解来标记控制器类。

请求页面和数据，使用@Controller注解，请求数据，使用@RestController注解。

### MVC设计
MVC是一种设计模式，用于将应用程序分为三个主要部分：模型（Model）、视图（View）和控制器（Controller）。

- 模型（Model）：表示应用程序的数据和业务逻辑。它负责处理应用程序的数据和业务规则，并将数据传递给视图。
- 视图（View）：表示应用程序的用户界面。它负责显示模型中的数据，并将用户输入传递给控制器。
- 控制器（Controller）：负责处理用户的输入，并将输入传递给模型进行处理，然后将处理结果传递给视图进行显示。

```
                    +------------------+
                    |    Controller    |
                    +------------------+
                       ^            \
                       | 1.HTTP请求   \ 2.请求信息
                       |              \
                       |               v
                    ( 用户 )      +-----------+
                       ^          |   Model   |
                       |          +-----------+
                       |               ^
                       | 5.HTTP响应    | 3.响应信息
                       |               |
                 +-----------+ <------+
                 |   View    |
                 +-----------+
                        ^
                        |
                   4.返回数据
```

### 路由映射
@RequestMapping是总父类，包括GetMapping、PostMapping、PutMapping、DeleteMapping等子类，用于定义路由映射。
所以：
`GetMapping("/users")`
等价于
```
@RequestMapping(
    value = "/hello",
    method = RequestMethod.GET
)
```
在Spring MVC中，可以使用@RequestMapping注解来定义路由映射。例如：

```
@RestController
public class UserController {

    @RequestMapping("/users")
    public  List<User> getUsers() {
        return userService.getUsers();
    }
} 
```

### 参数：
    value：指定路由的URL路径
    method：指定请求的方法，例如GET、POST、PUT、DELETE等
    consumes：指定请求的媒体类型，例如application/json、application/xml等
    produces：指定响应的媒体类型，例如application/json、application/xml等
    params：指定请求的参数，例如@RequestParam、@PathVariable等

    用法： `@GetMapping(value="/test", params="id")`

### 通配符
在Spring MVC中，可以使用通配符来定义路由映射。例如：
```
@GetMapping("/users/{id}")
public User getUser(@PathVariable("id") Long id) {
    return userService.getUserById(id);
}
```
或者
```
@GetMapping("/users/*.json")
```
在上面的例子中，`{id}`是一个路径变量，表示URL中的id部分。`@PathVariable`注解用于将URL中的id部分绑定到方法参数id上。或者用*来代替不确定的部分。（正则表达式）。
没有通配符的优先级高于有通配符的。

### 参数绑定

```
    @RequestMapping(value = "/name" , method = RequestMethod.GET)
    public String GetName(String FirstName)
    {
        return "你好" + FirstName;
    }

curl -X GET "http://localhost:8080/name?FirstName=张三"
```
上述代码会将URL中的参数FirstName绑定到方法参数FirstName上。Spring MVC会自动将URL中的参数匹配方法名。
Spring MVC不关心参数名，只关心参数类型。比如上述的参数 String FirstName 只匹配了URL中的参数FirstName，而不管参数名是什么，只要参数类型是String，Spring MVC就会自动将URL中的参数绑定到方法参数上。
? 标识查询参数开始的位置，& 标识查询参数之间的分隔符，例如：`/name?FirstName=Tom&age=18`

#### RequestParam的用法：

```
    @RequestMapping(value = "/gender" , method = RequestMethod.GET)
    public String GetGender(@RequestParam("FirstGender") String Gender)
    {
        return "你好" + Gender;
    }
```
上述代码会将URL中的参数FirstGender绑定到方法参数Gender上。@RequestParam注解用于将URL中的参数绑定到方法参数上。@RequestParam注解可以指定参数的默认值，例如：`@RequestParam("FirstGender") String Gender = "男";`。如果URL中没有指定参数FirstGender，那么默认值为"男"。

@RequestParam将路径中的参数转换为方法参数。

也就是说：FirstGender -> Gender 

访问：http://localhost:8080/gender?FirstGender=男时，实际调用的是 Gender 这个方法参数。

##### 参数 required 属性

@RequestParam注解有一个required属性，用于指定参数是否是必需的。默认情况下，required属性为true，表示参数是必需的。
如果URL中没有指定参数，那么Spring MVC会抛出400错误，比如`http://localhost:8080/gender`
如果required属性为false，表示参数不是必需的。如果URL中没有指定参数，那么Spring MVC会将参数的值设置为null。

```
    @RequestMapping(value = "/gender" , method = RequestMethod.GET)
    public String GetGender(@RequestParam(value = "FirstGender", required = false) String Gender)
    {
        return "你好" + Gender;
    }
```

#### @RequestBody 用法
@RequestBody注解用于将请求体中的JSON数据转换为Java对象。@RequestBody注解可以用于方法的参数上，也可以用于方法的返回值上。

```
    @RequestMapping(value = "/user" , method = RequestMethod.POST)
    public User PostUser(@RequestBody User user)
    {
        return user;
    }
```
上述代码会将请求体中的JSON数据转换为User对象，并将User对象作为方法的返回值。如果请求体中的JSON数据不符合User对象的格式，那么Spring MVC会抛出400错误。

## 3.文件上传
Spring MVC提供了文件上传的支持，可以通过MultipartFile接口来处理上传的文件。

```
    @PostMapping("/upload")
    public String up (String nickname, MultipartFile photo, HttpServletRequest request) throws IOException
    {
        System.out.println(nickname);
        System.out.println(photo.getOriginalFilename());
        System.out.println(photo.getContentType());

        String path = request.getServletContext().getRealPath("/upload");
        System.out.println(path);
        saveFile(photo,path);
        return "Upload Successful";
    }
```
### 参数 MulktipartFile 

是 Spring MVC 里的上传文件对象，表示用户上传的文件（图片/头像/pdf等）。
上传后，Spring 会把文件封装成 MultipartFile 对象，通过 MultipartFile 对象可以获取文件的原始文件名、文件类型、文件大小、文件内容等信息.

- MultipartFile.getOriginalFilename()：获取文件的原名。
- MultipartFile.getContentType()：获取文件的类型。
- MultipartFile.getSize()：获取文件的大小。
- MultipartFile.getInputStream()：获取文件的输入流。
- MultipartFile.transferTo(File dest)：将文件保存到指定的路径。

### HttpServletRequest request：

表示当前请求对象，里面包含整个HTTP请求的信息，比如请求头，Cookie等。
可以通过request对象获取请求的相关信息，如请求的路径、请求的方法、请求的参数等。

- request.getServletContext().getRealPath("/upload")：获取项目在服务器上的绝对路径。

getservletContext()：获取当前web应用的ServletContext对象，ServletContext对象是整个web应用的上下文对象，是整个Web项目的运行环境，里面包含项目路径，全局配置等。

### throws IOException

表示方法可能会抛出IOException异常，需要处理异常。

### 文件大小限制

在 Spring MVC 中，可以通过配置文件来限制上传文件的大小。在 Spring Boot 中，可以在 application.properties 文件中添加以下配置：

```
spring.servlet.multipart.max-file-size=10MB
spring.servlet.multipart.max-request-size=10MB
```

以上配置表示上传文件的最大大小为10MB，上传请求的最大大小为10MB。如果上传的文件大小超过了这个限制，就会抛出异常。



### 静态资源目录

`spring.web.resources.static-locations=`为静态资源目录配置，告诉SpringBoot在哪些目录寻找静态文件。

```
什么是静态资源？
不会改变，比如图片、js、css、html等文件。

比如访问：http://localhost:8080/images/logo.png
会返回图片，而不是执行Java代码。
```
默认情况下，Spring Boot 会自动将以下目录下的文件作为静态资源：

- /static
- /public
- /resources
- /META-INF/resources

#### static-locations 是做什么？

为自定义静态资源目录，告诉SpringBoot在哪些目录寻找静态文件。
比如：
```
spring.web.resources.static-locations=file:D:/upload/
```

以上配置表示让 SpringBoot 去 D:/upload 找静态文件。

如果需要添加其他的静态资源目录，可以在 application.properties 文件中添加以下配置：

```
spring.web.resources.static-locations=classpath:/static/,classpath:/public/
```

以上配置表示在 classpath:/static/ 和 classpath:/public/ 目录下寻找静态文件。

##### file: 和 classpath: 的区别

- file: 表示文件系统中的路径。
- classpath: 表示类路径下的路径。

比如：
```
spring.web.resources.static-locations=file:D:/upload/
```

以上配置表示让 SpringBoot 去 D:/upload 找静态文件。

```
spring.web.resources.static-locations=classpath:/static/
```

以上配置表示让 SpringBoot 去 classpath:/static/ 找静态文件。

## 4.拦截器

Spring Boot 拦截器是用于拦截请求的组件，可以在请求到达控制器之前或之后执行一些操作。
Spring Boot 拦截器可以用于实现登录验证、权限验证、日志记录等功能。

在进入Controller前后，插入你自己的逻辑。
```
浏览器请求
   ↓
过滤器(Filter)
   ↓
拦截器(Interceptor)
   ↓
Controller
   ↓
Service
   ↓
返回响应
```
- 适合做：
- 登录校验
- 权限判断
- 日志记录
- 请求耗时统计
- Token 校验
- API 限流
- 国际化
- 用户信息注入

#### 拦截器配置

Spring Boot 拦截器配置基于 HandlerInterceptor 接口来实现。
HandlerInterceptor 接口有三个方法：

- preHandle：在请求到达控制器之前执行，返回 true 表示继续执行，返回 false 表示拦截请求。
- postHandle：在请求到达控制器之后执行，但在视图渲染之前执行。
- afterCompletion：在请求完成之后执行，无论请求是否成功。

 

