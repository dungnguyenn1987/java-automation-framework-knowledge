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

public class EndPoints {
	
    private final RequestSpecification request;

    public EndPoints(String baseUrl) {
        RestAssured.baseURI = baseUrl;
        request = RestAssured.given();
        request.header("Content-Type", "application/json");
    }

    public IRestResponse<Token> authenticateUser(AuthorizationRequest authRequest) {
	RestAssured.baseURI = BASE_URL;
        RequestSpecification request = RestAssured.given();

        request.header("Content-Type", "application/json");
        Response response = request.body(authRequest).post(Route.generateToken());
        return new RestResponse(Token.class, response);
	}

    public IRestResponse<Books> getBooks() {
        RestAssured.baseURI = BASE_URL;
        RequestSpecification request = RestAssured.given();

        request.header("Content-Type", "application/json");
        Response response = request.get(Route.books());
        return new RestResponse(Books.class, response);
    }

    public IRestResponse<UserAccount> addBook(AddBooksRequest addBooksRequest, String token) {
        RestAssured.baseURI = BASE_URL;
        RequestSpecification request = RestAssured.given();
        request.header("Authorization", "Bearer " + token)
                .header("Content-Type", "application/json");

        Response response = request.body(addBooksRequest).post(Route.books());
        return new RestResponse(UserAccount.class, response);
    }

    public Response removeBook(RemoveBookRequest removeBookRequest, String token) {

        RestAssured.baseURI = BASE_URL;
        RequestSpecification request = RestAssured.given();

        request.header("Authorization", "Bearer " + token)
                .header("Content-Type", "application/json");

        return request.body(removeBookRequest).delete(Route.book());
    }

    public IRestResponse<UserAccount> getUserAccount(String userId, String token) {

        RestAssured.baseURI = BASE_URL;
        RequestSpecification request = RestAssured.given();

        request.header("Authorization", "Bearer " + token)
                .header("Content-Type", "application/json");

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
package stepDefinitions;

import apiEngine.EndPoints;
import apiEngine.IRestResponse;
import apiEngine.model.*;
import apiEngine.model.requests.*;
import apiEngine.model.responses.*;
import org.junit.Assert;
import cucumber.api.java.en.Given;
import cucumber.api.java.en.Then;
import cucumber.api.java.en.When;
import io.restassured.response.Response;

public class Steps {

    private final String USER_ID = "9b5f49ab-eea9-45f4-9d66-bcf56a531b85";    
    private Response response;
    private IRestResponse<UserAccount> userAccountResponse;
    private Book book;
    private final String BaseUrl = "https://bookstore.toolsqa.com";
    private EndPoints endPoints;
    
    @Given("^I am an authorized user$")
    public void iAmAnAuthorizedUser() {
    	endPoints = new EndPoints(BaseUrl);
    	AuthorizationRequest authRequest = new AuthorizationRequest("TOOLSQA-Test", "Test@@123");
    	endPoints.authenticateUser(authRequest);
    }

    @Given("^A list of books are available$")
    public void listOfBooksAreAvailable() {   	    	
    	IRestResponse<Books> booksResponse = endPoints.getBooks();
    	book = booksResponse.getBody().books.get(0);
    }

    @When("^I add a book to my reading list$")
    public void addBookInList() {
    	
        ISBN isbn = new ISBN(book.isbn);
        AddBooksRequest addBooksRequest = new AddBooksRequest(USER_ID, isbn);
        userAccountResponse = endPoints.addBook(addBooksRequest);
    }

    @Then("^The book is added$")
    public void bookIsAdded() {
        
    	Assert.assertTrue(userAccountResponse.isSuccessful());
        Assert.assertEquals(201, userAccountResponse.getStatusCode());

        Assert.assertEquals(USER_ID, userAccountResponse.getBody().userID);
        Assert.assertEquals(book.isbn, userAccountResponse.getBody().books.get(0).isbn);
    }

    @When("^I remove a book from my reading list$")
    public void removeBookFromList() {

        RemoveBookRequest removeBookRequest = new RemoveBookRequest(USER_ID, book.isbn);
        response = endPoints.removeBook(removeBookRequest);
    }

    @Then("^The book is removed$")
    public void bookIsRemoved() {
    	
        Assert.assertEquals(204, response.getStatusCode());

        userAccountResponse = endPoints.getUserAccount(USER_ID);
        Assert.assertEquals(200, userAccountResponse.getStatusCode());
        
        Assert.assertEquals(0, userAccountResponse.getBody().books.size());
    }

}
```

</details>
<br/>
