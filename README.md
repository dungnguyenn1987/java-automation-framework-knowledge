# Installation
<details>
	
* Install Java JDK (version 1.8 or later https://www.oracle.com/vn/java/technologies/downloads/#java21)
  * Set Environment variables:
  	* `JAVA_HOME=C:\Program Files\Java\jdk-21\`
   	* Add more Path `C:\Program Files\Java\jdk-21\bin`
  * Check version: `java –version`
* Download and extract Maven to any location (https://maven.apache.org/download.cgi)
  * Set Environment variables:
  	* `MAVEN_HOME=C:\Program Files\apache-maven-3.8.8`
   	* Add more Path =C:\Program Files\apache-maven-3.8.8\bin`
  * Check version: `mvn –version`
* Install Editor
  * Visual Studio Code with adding extensions
    * Extension Pack for Java
    * Cucumber (Gherkin) Full Support
    * And The extension supports the following test frameworks:
    	* JUnit 4 (v4.8.0+)
    	* JUnit 5 (v5.1.0+)
    	* TestNG (v6.9.13.3+)
  * OR Intellij Community Editor with plugins
	* Cucummber for Java
	* Cucumber for Groovy
  * OR Eclipse IDE

</details>
	
# Project Setup
<details>
	
* Initialize
	* VSCode (https://code.visualstudio.com/docs/java/java-testing)

![image](https://github.com/user-attachments/assets/ebab51da-1cb6-439d-ac47-3800d84b41ba)

	* Intellij
 
![image](https://github.com/user-attachments/assets/687de8c8-e495-4eb9-9fba-94038f00e900)

* Add dependencies to pom.xml

```java
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>org.example</groupId>
  <artifactId>JavaAutoTests</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>jar</packaging>

  <name>JavaAutoTests</name>
  <url>http://maven.apache.org</url>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>

  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.13.2</version>
    </dependency>
    <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter-api</artifactId>
      <version>5.11.3</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>io.rest-assured</groupId>
      <artifactId>rest-assured</artifactId>
      <version>5.5.0</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>io.cucumber</groupId>
      <artifactId>cucumber-java</artifactId>
      <version>7.20.1</version>
    </dependency>
    <dependency>
      <groupId>io.cucumber</groupId>
      <artifactId>cucumber-junit</artifactId>
      <version>7.20.1</version>
    </dependency>
    <dependency>
      <groupId>io.cucumber</groupId>
      <artifactId>cucumber-picocontainer</artifactId>
      <version>7.20.1</version>
    </dependency>
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.10.2</version>
    </dependency>
  </dependencies>
</project>

```
 
</details>

# How to run tests
<details>

* VSCode

![image](https://github.com/user-attachments/assets/1d9eee01-ccc5-4218-aa85-51eca20d0083)

* Intellij:

![image](https://github.com/user-attachments/assets/52fb41e8-5e3e-4fc1-925f-8face21d6b18)

</details>

# UI Tests
## Page Object Model (POM)
* a design pattern or a framework that we use in Selenium using which one can create an object repository of the different web elements across the application. By this way, the code becomes easy to maintain and reduces code duplicity
* In the Page Object Model framework, we create a class file for each web page. This class file consists of different web elements present on the web page
* Test scripts then use these elements to perform different actions

![image](https://github.com/user-attachments/assets/a921bc62-cac6-4144-9c63-827cab3fe7b6)


# API Tests
1. Create a Route class: contains URI and build full paths to server
<details>
<summary>Show scripts</summary>

```java
package apiEngine;

public class Route {

    private static final String BOOKSTORE = "/BookStore";
    private static final String ACCOUNT = "/Account";
    private static final String VERSION = "/v1";
    
    
    public static String generateToken(){
    	return ACCOUNT + VERSION + "/GenerateToken";
    }

    public static String books(){ 
    	return BOOKSTORE + VERSION + "/Books";
    }

    public static String book(){ 
    	return BOOKSTORE + VERSION + "/Book";
    }

    public static String userAccount(String userId){
    	return ACCOUNT + VERSION + "/User" + "/" + userId; 
    }

}
```

</details>
<br>

2. Create a POJO class for a Request Body
* Serialization of convert JSON request body to JAVA Object
* POJO (plain old Java object) is an ordinary Java object, not bound by any special restriction
* Use https://www.jsonschema2pojo.org/ to generate POJO
<details>
<summary>jsonschema2pojo</summary>

![image](https://github.com/user-attachments/assets/aa9d1044-c3e0-4856-ad12-1e547a0bcfdd)

</details>

<details>
<summary>Show scripts</summary>

```java
package apiEngine.model.requests;

public class AuthorizationRequest {

    public String username;
    public String password;

    public AuthorizationRequest(String username, String password) {
        this.username = username;
        this.password = password;
    }
}
```
```java
package apiEngine.model.requests;

import java.util.ArrayList;
import java.util.List;

public class AddBooksRequest {

    public String userId;
    public List<ISBN> collectionOfIsbns;
    
    //As of now this is for adding a single book, later we will add another constructor.
    //That will take a collection of ISBN to add multiple books
    public AddBooksRequest(String userId, ISBN isbn){
        this.userId = userId;
        collectionOfIsbns = new ArrayList<ISBN>();
        collectionOfIsbns.add(isbn);
    }

}
```

```java
package apiEngine.model.requests;

public class RemoveBookRequest {
    public String isbn;
    public String userId;

    public RemoveBookRequest(String userId, String isbn) {
    	this.userId = userId;
    	this.isbn = isbn;
    }
}
```

```java
package apiEngine.model.requests;

public class ISBN {
    public String isbn;

    public ISBN(String isbn) {
        this.isbn = isbn;
    }
}
```
</details>

<br>

3. Create a POJO class for a Response Body
* DeSerialization of convert JSON response body to JAVA Object
* Purpose

Change to Deserializing the Response body (in `Endpoint` class) to access specific info from jsonPath into JAVA Object

`String jsonString = response.asString();`

`String token = JsonPath.from(jsonString).get("token");`

To

`tokenResponse = response.getBody().as(Token.class);`

<details>
<summary>Show scripts</summary>

```java
package apiEngine.model;

public class Book {
    public String isbn;
    public String title;
    public String subTitle;
    public String author;
    public String published;
    public String publisher;
    public int pages;
    public String description;
    public String website;
}
```

```java
package apiEngine.model.responses;

import java.util.List;
import apiEngine.model.Book;

public class Books {
	 public List<Book> books;
}
```

```java
package apiEngine.model.responses;

public class Token {
	 public String token;
	 public String expires;
	 public String status;
	 public String result;
}
```

```java
package apiEngine.model.responses;

import java.util.List;
import apiEngine.model.Book;

public class UserAccount {
    public String userID;
    public String userName;
    public List<Book> books;
}
```
</details>
<br/>

4. Create a Generic Interface: interface capable of handling different response objects. To provide this parameterized value to a parameterized type, we implement this as a Generic Interface. This interface will contain all the methods we need when we operate on a REST Response.

<details>
<summary>Show scripts</summary>

```java
package apiEngine;

import io.restassured.response.Response;

public interface IRestResponse<T>{
		public T getBody();
		
		public String getContent();
		
		public int getStatusCode();
		
		public boolean isSuccessful();
		
		public String getStatusDescription();
		
		public Response getResponse();
		
		public Exception getException();
	}
```
```java
package apiEngine;

import io.restassured.response.Response;

public class RestResponse<T> implements IRestResponse<T> {
	private T data;
	private Response response;
	private Exception e;

	public RestResponse(Class<T> t, Response response) {
		this.response = response;
		try{
			this.data = t.newInstance();
		}catch (Exception e){
			throw new RuntimeException("There should be a default constructor in the Response POJO");
		}
	}
	
	public String getContent() {
		return response.getBody().asString();
	}

	public int getStatusCode() {
		return response.getStatusCode();
	}

	public boolean isSuccessful() {
		int code = response.getStatusCode();
		if( code == 200 || code == 201 || code == 202 || code == 203 || code == 204 || code == 205) return true;
		return false;
	}

	public String getStatusDescription() {
		return response.getStatusLine();
	}
	
	public Response getResponse() {
		return response;
	}
	

	public T getBody() {
		try {
			data = (T) response.getBody().as(data.getClass());
		}catch (Exception e) {
			this.e=e;
		}
		return data;
	}

	public Exception getException() {
		return e;
	}

}

```
</details>
<br/>

5. Create API Services

<details>
<summary>Show scripts</summary>

```java
package apiEngine;

import apiEngine.model.requests.AddBooksRequest;
import apiEngine.model.requests.AuthorizationRequest;
import apiEngine.model.requests.RemoveBookRequest;
import apiEngine.model.responses.Books;
import apiEngine.model.responses.Token;
import apiEngine.model.responses.UserAccount;
import io.restassured.RestAssured;
import io.restassured.response.Response;
import io.restassured.specification.RequestSpecification;
import org.apache.http.HttpStatus;

public class EndPoints {

    private final RequestSpecification request;

    public EndPoints(String baseUrl) {
        RestAssured.baseURI = baseUrl;
        request = RestAssured.given();
        request.header("Content-Type", "application/json");
    }

    public void authenticateUser(AuthorizationRequest authRequest) {
        Response response = request.body(authRequest).post(Route.generateToken());
        if (response.statusCode() != HttpStatus.SC_OK)
            throw new RuntimeException("Authentication Failed. Content of failed Response: " + response.toString() + " , Status Code : " + response.statusCode());

        Token tokenResponse = response.body().jsonPath().getObject("$", Token.class);
        request.header("Authorization", "Bearer " + tokenResponse.token);
    }

    public IRestResponse<Books> getBooks() {
        Response response = request.get(Route.books());
        return new RestResponse(Books.class, response);
    }

    public IRestResponse<UserAccount> addBook(AddBooksRequest addBooksRequest) {
        Response response = request.body(addBooksRequest).post(Route.books());
        return new RestResponse(UserAccount.class, response);
    }

    public Response removeBook(RemoveBookRequest removeBookRequest) {
        return request.body(removeBookRequest).delete(Route.book());
    }

    public IRestResponse<UserAccount> getUserAccount(String userId) {
        Response response = request.get(Route.userAccount(userId));
        return new RestResponse(UserAccount.class, response);
    }

}

```
</details>
<br/>

6. Call API Services in the Step Definitions or Tests (test layer)


<details>
<summary>Show scripts</summary>

```java
    @When("^I add a book to my reading list$")
    public void addBookInList() {

        ISBN isbn = new ISBN(book.isbn);
        AddBooksRequest addBooksRequest = new AddBooksRequest(USER_ID, isbn);
        userAccountResponse = endPoints.addBook(addBooksRequest, tokenResponse.token);
    }

```

</details>
<br/>

# Enhancements
## Sharing Context between Cucumber Step Definitions
* Dependency Injection (DI) Containers - it simply tells a DI container to instantiate your step definition classes and wire them up correctly. One of the supported DI containers is PicoContainer (need to add in pom.xml). Cucumber scans your classes with step definitions in them, passes them to PicoContainer, then asks it to create new instances for every scenario
* Create a Test Context class that containts all information your Steps file are using
 * PageObjects
 * WebDriver
 * BASE_URL
 * endPoints

```java
package cucumber;

import apiEngine.EndPoints;

public class TestContext {
	
	private String BASE_URL = "https://bookstore.toolsqa.com";
	private EndPoints endPoints;
	
	public TestContext() {
		endPoints = new EndPoints(BASE_URL);
	}
	
	 public EndPoints getEndPoints() {
        return endPoints;
    }
}
```
