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

> ### 【知識點1】@RequestBody 映射請求體到 java 方法的參數
>
> ### 【知識點2】日期類型參數的處理
>
> ### 【知識點3】@Valid 註解和 BindingResult 驗證請求參數的合法性並處理校驗結果

  	

### 【知識點1】@RequestBody 映射請求體到 java 方法的參數

#### 前端給的 json 資料透過 @RequestBody 將 json 資料建立成一個物件並為參數在 java 方法裡使用

#### 前端給的 json 資料(無論是透過瀏覽器AJAX還是APP HTTP Request)

```json
{"username":"David", "password":"12345"}
```



#### Spring Controller 中透過 @RequestBody 將 json 資料建立成一個物件並為參數在 java 方法裡使用

```java
@PostMapping
public User create(@RequestBody User user){ //Here
    System.out.println(user.getUsername());
    System.out.println(user.getPassword());
    
    user.setId(1);
    return user;
}
```

#### 第 2 行 透過 @RequestBody 將 json 資料建立成一個物件並為參數在 java 方法裡使用









### 【知識點2】日期類型參數的處理

#### 建議前端傳時間戳(例：new Date().getTime())給後端，由後端來自行決定要用什麼日期格式來顯示給使用者。反之由後端傳給前端也是用時間戳的方式。











### 【知識點3】@Valid 註解和 BindingResult 驗證請求參數的合法性並處理校驗結果

#### 用於驗證使用者輸入的資料合法性

#### 在類別上加入 hibernate validator annotation───@NotBlank，標示此屬性(field or variable)不能是空字串

```java
public class User{
    public interface UserSimpleView {};
    public interface UserDetailView extends UserSimpleView {};

    private String username;

    @NotBlank // ~ Here -------------------------------------
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

#### 第 7 行表示 password 不能為空，限制使用者一定要傳入password，在 Controller 類必須配合 @Valid 來使用才有校驗的效果

```java
@PostMapping
public User create(@Valid @RequestBody User user){ // ~ Here --------------
    System.out.println(user.getUsername());
    System.out.println(user.getPassword());
    
    user.setId(1);
    return user;
}
```

#### 第 2 行加入 @Valid 它就會根據剛剛在 User 類寫的 @NotBlank 來進行校驗



#### 目前通不過 @Valid + @NotBlank 校驗的資料，會直接不執行此 Controller 映射的 java 方法。但如果我們依然要進去此 java 方法記錄一些資訊(例如：哪個用戶在什麼時間點未輸入就發出請求)，此時該如何做呢？(既然驗證資料合法性又能進入方法執行一些代碼)

#### 此時就需要 BindingResult 來處理

#### @Valid 必須跟 BindingResult 配合，任何通不過 @Valid 校驗的錯誤結果都會由 BindingResult 來處理

```java
@PostMapping
public User create(@Valid @RequestBody User user, BindingResult errors){ // ~ Here
    
    if(errors.hasErrors()){ 
        errors.gasAllErrors.stream().forEach(error -> System.out.println(error.getDefaultMessage()));
    }
    
    System.out.println(user.getUsername());
    System.out.println(user.getPassword());
    
    user.setId(1);
    return user;
}
```

#### 第 2 行帶入 BindingResult 參數，好用來處理校驗的結果

#### 第 4、5、6 行輸入錯誤的結果，以此案例來看傳入的密碼為空，會印出 "may not be empty" 的錯誤訊息