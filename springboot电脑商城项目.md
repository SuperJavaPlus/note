## 项目

### 上传图像BUG

#### 1.更改默认的大小限制

SpringMVC默认的为1MB文件可以进行上传，手动的修改SpringMVC默认上传

方式一：可以直接在配置文件中配置

```yaml
spring:
  servlet:
    multipart:
      max-file-size: 10MB
```

方式二：需要采用Java代码的形式来设置文件上传大小的限制，主类中进行配置

```java
@Configuration
@SpringBootApplication
public class StoresApplication {

    public static void main(String[] args) {
        SpringApplication.run(StoresApplication.class, args);
    }
	
    @Bean
    public MultipartConfigElement getMultipartConfigElement() {
        //创建一个配置的工厂类对象
        MultipartConfigFactory factory = new MultipartConfigFactory();

        //设置需要创建的对象的相关信息
        factory.setMaxFileSize(DataSize.of(10, DataUnit.MEGABYTES));
        factory.setMaxRequestSize(DataSize.of(15,DataUnit.MEGABYTES);

        //通过工厂类创建MultipartConfig
        return factory.createMultipartConfig();
    }

}

```

#### 2.显示图像

在页面中通过Ajax请求来提交文件，提交完成后返回了json串，解析出data中的数据，设置到img头像标签的src属性上就可以了

- serialize():将表单数据自动拼接成key-value的结构进行提交给服务器，一般提交时普通的控件类型的数据（text/password/radio/checkbox）

- FormData类：将表单的数据保持原有的结构进行数据的提交。

  ```js
  new FormData($("#from")[0]); //文件类型的数据可以使用FormData对象进行存储
  ```

- ajax默认处理数据是按照字符串形式进行处理

```js
$("#btn-change-avatar").click(function () {
   $.ajax({
      url: "users/change_avatar",
      type: "POST",
      data: new FormData($("#form-change-avatar")[0]),
      dataType: "JSON",
      processData: false,  //关闭默认的处理数据的形式
      contentType: false, //关闭默认的提交数据的形式
      success: function (json) {
         
      }
   })
})
```

