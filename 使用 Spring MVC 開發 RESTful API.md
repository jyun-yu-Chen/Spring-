[TOC]



# 使用 Spring MVC 開發 RESTful API

## 學習內容

- ### 使用 Spring MVC 編寫 RESTful API

- ### 使用 Spring MVC 處理其它 web 應用常見的需求和場景

- ### RESTful API 開發常用輔助框架



## 一般常見的API請求URL

|      | 一般請求寫法                                          | RESTful寫法                                                  |
| ---- | ----------------------------------------------------- | ------------------------------------------------------------ |
| 查詢 | /user/query?name=tom　　              GET             | /user?name=tom     GET                                       |
| 詳情 | /user/getInfo?id=1　　　　                GET         | /user/1                       GET                            |
| 創建 | /user/create?name=tom                     POST        | /user                           POST                         |
| 修改 | /user/update?id=1&name=jerry        POST              | /user/1                        PUT                           |
| 刪除 | /user/delete?id=1                                 GET | /user/1                        DELETE                        |
|      | 用URL描述資源                                         | 用HTTP方法描述行為，使用HTTP狀態碼表示不同的結果<br />使用json交換數據 |



## 編寫第一個 RESTful API

> ### 編寫針對 RESTful API 的測試用例
>
> ### 使用註解聲明 RESTful API 
>
> ### 在 RESTful API 中傳遞參數





### 編寫針對 RESTful API 的測試用例

#### 使用 Spring Boot 測試並偽造 MVC 環境來測試 RESTful API

```java
@RunWith(SpringJUnit4ClassRunner.class)  
@SpringBootTest(classes = Application.class)  
...省略

@Autowired  
private WebApplicationContext webApplicationContext;

//創建MockMvc類的物件
private MockMvc mockmvc; 

@Before
public void setup(){
    //構建出偽造的MVC環境
	mockmvc = MockMvcBuilders.webAppContextSetup(webApplicationContext).build();
}

@Tset
public void whenQuerySuccess() throws Exception {
    
    mockmvc.perform(MockMvcRequestBuilders.get(uri)
           .        )
    
    
    
    
}
```





## 二、以「用戶詳情請求(編寫用戶詳情服務)」為例，學習下列的知識點

> ### 【知識點1】@PathVariable 映射 url 片段到 java 方法的參數
>
> ### 【知識點2】在 url 聲明中使用正則表達式
>
> ### 【知識點3】@JsonView 控制 json 輸出內容





### 【知識點1】@PathVariable 映射 url 片段到 java 方法的參數

```java
@RequestMapping(value = "/user/{id}", method = RequestMethod.GET)
public User getInfo(@PathVariable String id){ 
    User user = new User();
    user.setUserName("jyun-yu");
    return user;
}
```

#### 第2行使用 @PathVariable 會將 url 中的 「{id}」的值傳入 java 方法中的 id







### 【知識點2】在 url 聲明中使用正則表達式

```java
@RequestMapping(value = "/user/{id:\\d+}", method = RequestMethod.GET)
public User getInfo(@PathVariable String id){ 
    User user = new User();
    user.setUserName("jyun-yu");
    return user;
}
```

#### 第1行的 url 使用 「{id:\\\d+}」正則表達式，指定傳進來的值只能為數字









### 【知識點3】@JsonView 控制 json 輸出內容

#### 能夠限定返回的資料只包含哪些、哪些資料不回傳，使用 @JsonView 在 User Class 做資料回傳的限制。

#### @JsonView 使用步驟

> 1. #### 使用接口(介面)來聲明多個視圖
>
> 2. #### 在值對象的 get 方法上指定視圖
>
> 3. #### 在 Controller 方法上指定視圖





#### 1.使用接口(介面)來聲明多個視圖

```java
public class User{
    
    public interface UserSimpleView {};
    public interface UserDetailView extends UserSimpleView {};
        
    private String username;
    
    private String password;
    
    public String getUsername(){
        return username;
    }
    
    public void setUsername(String username){
        this.username = username;
    }
    
     public String getPassword(){
        return password;
    }
    
    public void setPassword(String password){
        this.password = password;
    }
    
}
```

#### 第3、4行使用接口(介面)來聲明多個視圖







#### 2.在值對象的 get 方法上指定視圖

```java
public class User{
    public interface UserSimpleView {};
    public interface UserDetailView extends UserSimpleView {};

    private String username;

    private String password;

    @JsonView(UserSimpleView.class)
    public String getUsername(){
        return username;
    }

    public void setUsername(String username){
        this.username = username;
    }

     @JsonView(UserDetailView.class)
     public String getPassword(){
        return password;
    }

    public void setPassword(String password){
        this.password = password;
	}
}
```

#### 第9與18行在值對象的 get 方法上指定視圖







#### 3.在 Controller 方法上指定視圖

```java
@RequestMapping(value = "/user/{id:\\d+}", method = RequestMethod.GET)
@JsonView(User.UserDetailView.class)
public User getInfo(@PathVariable String id){ 
    User user = new User();
    user.setUserName("jyun-yu");
    return user;
}
```

#### 第2行在 Controller 方法上指定視圖，此 getInfo 方法返回 user 時會返回password資料，因為在 User Class 中有指定 getPassword 的 JsonView 為 UserDetailView：

```java
@JsonView(UserDetailView.class)
public String getPassword(){
	return password;
}
```









## 三、用戶創建請求

### 【知識點1】@RequestBody 映射請求體到 java 方法的參數

### 【知識點2】日期類型









