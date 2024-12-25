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

# Projec Overview
<details>
	
![image](https://github.com/user-attachments/assets/bbd11987-9747-4266-b326-92e814097d3a)

</details>

# Common Components
### 1. Read Configurations from Property File
<details>
.properties files are mainly used in Java programs to maintain project configuration data, api, database config or project settings, etc. Each parameter in properties file is stored as a pair of strings, in key-value pair format, where each key is on one line. You can easily read properties from some file using an object of type Properties. Using properties file is we can configure things that are prone to change over a period of time without need of changing anything in code.

```
# Configuration.properties
environment=qa
browser=chrome
windowMaximize=true
driverPath=C:\\ToolsQA\\Libs\\Drivers\\chromedriver.exe
implicitlyWait=20
url=http://www.demoqa.com
testDataResourcePath=src/test/resources/testDataResources/

baseUrl=https://bookstore.toolsqa.com
userId=79617b93-711a-4b7d-a62f-9c7563a1b3d2
```

```java
package enums;

public enum DriverType {
    FIREFOX,
    CHROME,
    IE
}
```

```java
package enums;

public enum EnvironmentType {
    DEV,
    QA,
    UAT
}
```

```java
package dataProviders;

import java.io.BufferedReader;
import java.io.FileNotFoundException;
import java.io.FileReader;
import java.io.IOException;
import java.util.Properties;

import enums.DriverType;
import enums.EnvironmentType;

public class ConfigFileReader {
    private Properties properties;
    private final String propertyFilePath= "configs//Configuration.properties";

    public ConfigFileReader(){
        BufferedReader reader;
        try {
            reader = new BufferedReader(new FileReader(propertyFilePath));
            properties = new Properties();
            try {
                properties.load(reader);
                reader.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
            throw new RuntimeException("Configuration.properties not found at " + propertyFilePath);
        }
    }

    public String getDriverPath(){
        String driverPath = properties.getProperty("driverPath");
        if(driverPath!= null) return driverPath;
        else throw new RuntimeException("Driver Path not specified in the Configuration.properties file for the Key:driverPath");
    }

    public long getImplicitlyWait() {
        String implicitlyWait = properties.getProperty("implicitlyWait");
        if(implicitlyWait != null) {
            try{
                return Long.parseLong(implicitlyWait);
            }catch(NumberFormatException e) {
                throw new RuntimeException("Not able to parse value : " + implicitlyWait + " in to Long");
            }
        }
        return 30;
    }

    public String getApplicationUrl() {
        String url = properties.getProperty("url");
        if(url != null) return url;
        else throw new RuntimeException("Application Url not specified in the Configuration.properties file for the Key:url");
    }

    public DriverType getBrowser() {
        String browserName = properties.getProperty("browser");
        if(browserName == null || browserName.equals("chrome")) return DriverType.CHROME;
        else if(browserName.equalsIgnoreCase("firefox")) return DriverType.FIREFOX;
        else if(browserName.equals("iexplorer")) return DriverType.IE;
        else throw new RuntimeException("Browser Name Key value in Configuration.properties is not matched : " + browserName);
    }

    public EnvironmentType getEnvironment() {
        String environmentName = properties.getProperty("environment");
        if(environmentName == null || environmentName.equalsIgnoreCase("dev")) return EnvironmentType.DEV;
        else if(environmentName.equals("qa")) return EnvironmentType.QA;
        else if(environmentName.equals("uat")) return EnvironmentType.UAT;
        else throw new RuntimeException("Environment Type Key value in Configuration.properties is not matched : " + environmentName);
    }

    public Boolean getBrowserWindowSize() {
        String windowSize = properties.getProperty("windowMaximize");
        if(windowSize != null) return Boolean.valueOf(windowSize);
        return true;
    }

    public String getTestDataResourcePath(){
        String testDataResourcePath = properties.getProperty("testDataResourcePath");
        if(testDataResourcePath!= null) return testDataResourcePath;
        else throw new RuntimeException("Test Data Resource Path not specified in the Configuration.properties file for the Key:testDataResourcePath");
    }
    public String getBaseUrl() {
        String baseUrl = properties.getProperty("baseUrl");
        if(baseUrl != null) return baseUrl;
        else throw new RuntimeException("base_Url not specified in the Configuration.properties file.");
    }

    public String getUserID() {
        String userId = properties.getProperty("userId");
        if(userId != null) return userId;
        else throw new RuntimeException("user_Id not specified in the Configuration.properties file.");
    }
}
```

</details>

### 2. Apply Singleton Design Pattern
<details>
	
Singleton's purpose is to control object creation, limiting the number of objects to only one. Since there is only one Singleton instance, any instance fields of a Singleton will occur only once per class, just like static fields. To implement Singleton pattern, we have have the following common concepts.

* Private constructor to restrict instantiation of the class from other classes.
* Private static variable of the same class that is the only instance of the class.
* Public static method that returns the instance of the class, this is the global access point for outer world to get the instance of the singleton class.

```java
package managers;

import dataProviders.ConfigFileReader;
import dataProviders.JsonDataReader;

public class FileReaderManager {

    private static FileReaderManager fileReaderManager = new FileReaderManager();
    private static ConfigFileReader configFileReader;
    private static JsonDataReader jsonDataReader;

    private FileReaderManager() {
    }

    public static FileReaderManager getInstance( ) {
        return fileReaderManager;
    }

    public ConfigFileReader getConfigReader() {
        return (configFileReader == null) ? new ConfigFileReader() : configFileReader;
    }

    public JsonDataReader getJsonReader(){
        return (jsonDataReader == null) ? new JsonDataReader() : jsonDataReader;
    }
}
```

</details>

### 3. Share data between Cucumber Step Definitions
<details>

* Scenario Context in cucumber for sharing data between our Step Definitions. It holds the test data information explicitly, store values in a key-value pair between the steps. 

```java
package enums;

public enum Context {
    BOOK,
    USER_ID,
    USER_ACCOUNT_RESPONSE,
    BOOK_REMOVE_RESPONSE,
    PRODUCT_NAME;
}

```

```java
package cucumber;

import enums.Context;
import java.util.HashMap;
import java.util.Map;

public class ScenarioContext {

    private Map<String, Object> scenarioContext;

    public ScenarioContext(){
        scenarioContext = new HashMap<String, Object>();
    }

    public void setContext(Context key, Object value) {
        scenarioContext.put(key.toString(), value);
    }

    public Object getContext(Context key){
        return scenarioContext.get(key.toString());
    }

    public Boolean isContains(Context key){
        return scenarioContext.containsKey(key.toString());
    }
}
```

</details>

### 4. Sharing Sate between Cucumber Step Definitions
<details>

Refer https://www.thinkcode.se/blog/2017/04/01/sharing-state-between-steps-in-cucumberjvm-using-picocontainer

**Sharing state between scenarios**

Scenarios must be independent of one other so it is important that state is NOT shared between scenarios. Accidentally leaking state (Rò rỉ trạng thái) from one scenario into others makes your scenarios brittle and also difficult to run in isolation.

To prevent accidentally leaking state between scenarios:

- Avoid using global or static variables. It is unfortunately very easy for information to leak from one scenario to another. Static fields are not cleared while the JVM is running. To clear them, you would either have to reset them manually or restart the JVM. Both ways are cumbersome.
- Make sure you clean your database in a Before hook.
- If you share a browser between scenarios, delete cookies in a Before hook.

**Sharing state between steps**

A scenario in Gherkin is created by steps. Each step depends on previous steps. This means that we must be able to share state between steps. If You have added a bunch of scenarios to your project and finding your way around in the step definition class (1 class) is getting harder and harder. The problem with large classes are that they

- are not coherent, they are about a lot of different things at the same time
- don't follow the single responsibility principle, there are probably many reasons why a class has to be changed
- are messy, it is hard to find what you need

One way to split the steps may be according to the domain concept they work on. The next problem you will have to solve is to handle a shared state between the steps. The solution is to inject a common object in each class with steps An object that is recreated every time a new scenario is executed. Each scenario has a fresh world and leakage between scenarios through the world object is unlikely. Dependency injection can be done in many ways. A simple solution is to to inject dependencies through the constructor. This is sometimes referred to as Constructor Dependency Injection, CDI.
	
**Dependency Injection (DI) Containers** - it simply tells a DI container to instantiate your step definition classes and wire them up correctly. One of the supported DI containers is PicoContainer (need to add PicoContainer in pom.xml). Cucumber scans your classes with step definitions in them, passes them to PicoContainer, then asks it to create new instances for every scenario
* Create a Test Context class that containts all information your Steps file are using
  	* PageObjectManager 
  	* WebDriverManager 
  	* scenarioContext
  	* EndPoints

```java
package cucumber;

import managers.FileReaderManager;
import managers.PageObjectManager;
import managers.WebDriverManager;
import apiEngine.EndPoints;
import enums.Context;

public class TestContext {
    private ScenarioContext scenarioContext;
    private WebDriverManager webDriverManager;
    private PageObjectManager pageObjectManager;
    private EndPoints endPoints;

    public TestContext() {
        scenarioContext = new ScenarioContext();
        webDriverManager = new WebDriverManager();
        pageObjectManager = new PageObjectManager(webDriverManager.getDriver());
        endPoints = new EndPoints(FileReaderManager.getInstance().getConfigReader().getBaseUrl());
        scenarioContext.setContext(Context.USER_ID, FileReaderManager.getInstance().getConfigReader().getUserID());
    }

    public WebDriverManager getWebDriverManager() {
        return webDriverManager;
    }

    public PageObjectManager getPageObjectManager() {
        return pageObjectManager;
    }

    public EndPoints getEndPoints() {
        return endPoints;
    }
    public ScenarioContext getScenarioContext() {
        return scenarioContext;
    }

}
```

</details>

### 5. Hooks

<details>
	
Hooks are blocks of code that can run at various points in the Cucumber execution cycle. They are typically used for setup and teardown of the environment before and after each scenario.

```java
package stepDefinitions;

import cucumber.TestContext;
import io.cucumber.java.After;
import io.cucumber.java.Before;

public class Hooks {

    TestContext testContext;

    public Hooks(TestContext context) {
        testContext = context;
    }

    @Before
    public void BeforeSteps() {
		/*What all you can perform here
			Starting a webdriver
			Setting up DB connections
			Setting up test data
			Setting up browser cookies
			Navigating to certain page
			or anything before the test
		*/
    }

    @After
    public void AfterSteps() {
        testContext.getWebDriverManager().closeDriver();
    }

}
```

</details>

### 6. Runner

<details>
	
A runner class that will connect the specification in Gherkin with the steps implemented in Java. The runner class can be called anything but the Maven test runner searches the class path for classes that starts or ends with the word test. I prefer classes that ends with the word test. This means that naming it RunCukesTest will allow the test runner to find it and execute it as a part of the regular Maven build. It will be executed during the test phase.

This executable specification will be executed when you do `mvn test`

```java
package runners;

import cucumber.api.junit.Cucumber;
import org.junit.runner.RunWith;

@RunWith(Cucumber.class)
public class RunCukesTest {
}
```

</details>

# Components for UI Tests
### 1. Design WebDriver Manager
<details>
Some called it as WebDriver Factory or Browser Factory. This is the way for handling the logic of creating WebDriver dynamically by configuration when you want to change the browser run

```java
package managers;

import java.util.concurrent.TimeUnit;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.firefox.FirefoxDriver;
import org.openqa.selenium.ie.InternetExplorerDriver;
import enums.DriverType;
import enums.EnvironmentType;

public class WebDriverManager {
    private WebDriver driver;
    private static DriverType driverType;
    private static EnvironmentType environmentType;
    private static final String CHROME_DRIVER_PROPERTY = "webdriver.chrome.driver";

    public WebDriverManager() {
        driverType = FileReaderManager.getInstance().getConfigReader().getBrowser();
        environmentType = FileReaderManager.getInstance().getConfigReader().getEnvironment();
    }

    public WebDriver getDriver() {
        if(driver == null) driver = createDriver();
        return driver;
    }

    private WebDriver createDriver() {
        switch (driverType) {
            case FIREFOX : driver = new FirefoxDriver();
                break;
            case CHROME :
                System.setProperty(CHROME_DRIVER_PROPERTY, FileReaderManager.getInstance().getConfigReader().getDriverPath());
                driver = new ChromeDriver();
                break;
            case IE :
		driver = new InternetExplorerDriver();
                break;
        }

        if(FileReaderManager.getInstance().getConfigReader().getBrowserWindowSize()) driver.manage().window().maximize();
        driver.manage().timeouts(FileReaderManager.getInstance().getConfigReader().getImplicitlyWait(), TimeUnit.SECONDS);
        return driver;
    }

    public void closeDriver() {
        driver.close();
        driver.quit();
    }

}

```

</details>

### 2. Create Page Objests

<details>
To better manage the code and to improve the re-usability, this pattern suggests us to divided an application in different pages or a single page into sub pages in Page Object Pattern (POM)

```java
package pageObjects;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.support.PageFactory;

public class HomePage {
	WebDriver driver;
	
	public HomePage(WebDriver driver) {
		this.driver = driver;
		PageFactory.initElements(driver, this);
	}
	
	public void perform_Search(String search) {
		driver.navigate().to("https://shop.demoqa.com/?s=" + search + "&post_type=product");
	}
	
	public void navigateTo_HomePage() {
		driver.get("https://www.shop.demoqa.com");
	}

}
```

```java
package pageObjects;

import java.util.List;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.support.FindAll;
import org.openqa.selenium.support.FindBy;
import org.openqa.selenium.support.How;
import org.openqa.selenium.support.PageFactory;

public class ProductListingPage {
    WebDriver driver;

    public ProductListingPage(WebDriver driver) {
        this.driver = driver;
        PageFactory.initElements(driver, this);
    }

    @FindBy(how = How.CSS, using = "button.single_add_to_cart_button")
    private WebElement btn_AddToCart;

    @FindAll(@FindBy(how = How.CSS, using = ".noo-product-inner"))
    private List<WebElement> prd_List;

    public void clickOn_AddToCart() {
        btn_AddToCart.click();
    }

    public void select_Product(int productNumber) {
        prd_List.get(productNumber).click();
    }

    public String getProductName(int productNumber) {
        return prd_List.get(productNumber).findElement(By.cssSelector("h3")).getText();
    }

}
```

```java
package pageObjects;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.support.FindBy;
import org.openqa.selenium.support.How;
import org.openqa.selenium.support.PageFactory;

public class CartPage {
	
	public CartPage(WebDriver driver) {
		PageFactory.initElements(driver, this);
	}
	
	@FindBy(how = How.CSS, using = ".cart-button") 
	private WebElement btn_Cart;
	
	@FindBy(how = How.CSS, using = ".checkout-button.alt") 
	private WebElement btn_ContinueToCheckout;
	
	
	public void clickOn_Cart() {
		btn_Cart.click();
	}
	
	public void clickOn_ContinueToCheckout(){
		btn_ContinueToCheckout.click();
		try { Thread.sleep(5000);}
		catch (InterruptedException e) {}
	}
	
}
```

```java
package pageObjects;


import java.util.List;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.support.FindAll;
import org.openqa.selenium.support.FindBy;
import org.openqa.selenium.support.How;
import org.openqa.selenium.support.PageFactory;

import testDataTypes.Customer;

public class CheckoutPage {
	WebDriver driver;
	
	public CheckoutPage(WebDriver driver) {
		this.driver = driver;
	    PageFactory.initElements(driver, this);
	}
	
	@FindBy(how = How.CSS, using = "#billing_first_name") 
	private WebElement txtbx_FirstName;
	
	@FindBy(how = How.CSS, using = "#billing_last_name") 
	private WebElement txtbx_LastName;
	
	@FindBy(how = How.CSS, using = "#billing_email") 
	private WebElement txtbx_Email;
	
	@FindBy(how = How.CSS, using = "#billing_phone") 
	private WebElement txtbx_Phone;
	
	@FindBy(how = How.CSS, using = "#billing_country_field .select2-arrow") 
	private WebElement drpdwn_CountryDropDownArrow;
	
	@FindBy(how = How.CSS, using = "#billing_state_field .select2-arrow") 
	private WebElement drpdwn_CountyDropDownArrow;
	
	@FindAll(@FindBy(how = How.CSS, using = "#select2-drop ul li"))
	private List<WebElement> country_List;	
	
	@FindBy(how = How.CSS, using = "#billing_city") 
	private WebElement txtbx_City;
	
	@FindBy(how = How.CSS, using = "#billing_address_1") 
	private WebElement txtbx_Address;
	
	@FindBy(how = How.CSS, using = "#billing_postcode") 
	private WebElement txtbx_PostCode;
	
	@FindBy(how = How.CSS, using = "#ship-to-different-address-checkbox") 
	private WebElement chkbx_ShipToDifferetAddress;
	
	@FindAll(@FindBy(how = How.CSS, using = "ul.wc_payment_methods li"))
	private List<WebElement> paymentMethod_List;	
	
	@FindBy(how = How.CSS, using = "#terms.input-checkbox") 
	private WebElement chkbx_AcceptTermsAndCondition;
	
	@FindBy(how = How.CSS, using = "#place_order") 
	private WebElement btn_PlaceOrder;
	
	
	public void enter_Name(String name) {
		txtbx_FirstName.sendKeys(name);
	}
	
	public void enter_LastName(String lastName) {
		txtbx_LastName.sendKeys(lastName);
	}

	public void enter_Email(String email) {
		txtbx_Email.sendKeys(email);
	}
	
	public void enter_Phone(String phone) {
		txtbx_Phone.sendKeys(phone);
	}
	
	public void enter_City(String city) {
		txtbx_City.sendKeys(city);
	}
	
	public void enter_Address(String address) {
		txtbx_Address.sendKeys(address);
	}
	
	public void enter_PostCode(String postCode) {
		txtbx_PostCode.sendKeys(postCode);
	}
	
	public void check_ShipToDifferentAddress(boolean value) {
		if(!value) chkbx_ShipToDifferetAddress.click();
		try { Thread.sleep(5000);}
		catch (InterruptedException e) {}
	}
	
	public void select_Country(String countryName) {
		drpdwn_CountryDropDownArrow.click();
		try { Thread.sleep(2000);}
		catch (InterruptedException e) {}

		for(WebElement country : country_List){
			if(country.getText().equals(countryName)) {
				country.click();	
				try { Thread.sleep(3000);}
				catch (InterruptedException e) {}
				break;
			}
		}
	}
	
	public void select_County(String countyName) {
		drpdwn_CountyDropDownArrow.click();
		try { Thread.sleep(2000);}
		catch (InterruptedException e) {}

		for(WebElement county : country_List){
			if(county.getText().equals(countyName)) {
				county.click();	
				try { Thread.sleep(3000);}
				catch (InterruptedException e) {}
				break;
			}
		}
	}
	
	public void select_PaymentMethod(String paymentMethod) {
		if(paymentMethod.equals("CheckPayment")) {
			paymentMethod_List.get(0).click();
		}else if(paymentMethod.equals("Cash")) {
			paymentMethod_List.get(1).click();
		}else {
			new Exception("Payment Method not recognised : " + paymentMethod);
		}
		try { Thread.sleep(3000);}
		catch (InterruptedException e) {}
		
	}
	
	public void check_TermsAndCondition(boolean value) {
		if(value) chkbx_AcceptTermsAndCondition.click();
	}
	
	public void clickOn_PlaceOrder() {
		btn_PlaceOrder.submit();
	}
	
	
	public void fill_PersonalDetails(Customer customer) {
		enter_Name(customer.firstName);
		enter_LastName(customer.lastName);
		enter_Phone(customer.phoneNumber.mob);
		enter_Email(customer.emailAddress);
		enter_City(customer.address.city);
		enter_Address(customer.address.streetAddress);
		enter_PostCode(customer.address.postCode);
		select_Country(customer.address.country);
		select_County(customer.address.county);		
	}

}
```

</details>

### 3. Create Page Object Manager

<details>
In the case of multiple-step definition files, we will be creating an object of Pages again and again and make code duplication. To avoid this situation, we can create a Page Object Manager. The duty of the Page Object Manager is to create the page's object and also to make sure that the same object should not be created again and again. But to use a single object for all the step definition files.

```java
package managers;

import org.openqa.selenium.WebDriver;
import pageObjects.CartPage;
import pageObjects.CheckoutPage;
import pageObjects.ConfirmationPage;
import pageObjects.HomePage;
import pageObjects.ProductListingPage;

public class PageObjectManager {
	private WebDriver driver;
	private ProductListingPage productListingPage;
	private CartPage cartPage;
	private HomePage homePage;
	private CheckoutPage checkoutPage;
	private ConfirmationPage confirmationPage;	

	public PageObjectManager(WebDriver driver) {
		this.driver = driver;
	}

	public HomePage getHomePage(){
		return (homePage == null) ? homePage = new HomePage(driver) : homePage;
	}

	

	public ProductListingPage getProductListingPage() {
		return (productListingPage == null) ? productListingPage = new ProductListingPage(driver) : productListingPage;
	}

	public CartPage getCartPage() {
		return (cartPage == null) ? cartPage = new CartPage(driver) : cartPage;
	}

	public CheckoutPage getCheckoutPage() {
		return (checkoutPage == null) ? checkoutPage = new CheckoutPage(driver) : checkoutPage;
	}
}
```
</details>
	
# Components for API Tests
### 1. Create a Route class

<details>
Contains URI and build full paths to server

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

### 2. Create a POJO class for a Request Body

<details>
	
* serialize object to JSON and send it with the request.
* POJO (plain old Java object) is an ordinary Java object, not bound by any special restriction
* Use https://www.jsonschema2pojo.org/ to generate POJO

![image](https://github.com/user-attachments/assets/aa9d1044-c3e0-4856-ad12-1e547a0bcfdd)

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

### 3. Create a POJO class for a Response Body

<details>
* DeSerialization of convert JSON response body to JAVA Object
* Purpose

Change to Deserializing the Response body (in `Endpoint` class) to access specific info from jsonPath into JAVA Object

`String jsonString = response.asString();`

`String token = JsonPath.from(jsonString).get("token");`

To

`tokenResponse = response.getBody().as(Token.class);`

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

### 4. Create a Generic Interface

<details>
	
Interface capable of handling different response objects. To provide this parameterized value to a parameterized type, we implement this as a Generic Interface. This interface will contain all the methods we need when we operate on a REST Response.

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

### 5. Create API Services

<details>
	
We abstract the logic of communication with the server into a separate class. The class will contain methods that take the required request parameters and send back a response received from the server. Each method correspond to each API call that defined in your appication (e.g: generateToken, getUser, addBook...)

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

### 6. Call API Services in the Step Definitions or Tests (test layer)


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

# Features
<details>
<summary>UI</summary>
	
 ```java
Feature: Automated End2End Tests
  Description: The purpose of this feature is to test End 2 End integration.

  Scenario Outline: Customer place an order by purchasing an item from search
    Given user is on Home Page
    When he search for "dress"
    And choose to buy the first item
    And moves to checkout from mini cart
    And enter "<customer>" personal details on checkout page
    And select same delivery address
    And select payment method as "check" payment
    And place the order
    Examples:
      |customer|
      |Lakshay|
```
</details>

<details>
<summary>API</summary>
	
 ```java
Feature: Book Application API
  Background: User generates token for Authorisation
    Given I am an authorized user

  Scenario: the Authorized user can Add and Remove a book.
    Given A list of books are available
    When I add a book to my reading list
    Then The book is added
```

</details>

# Step Definitions

<details>
<summary>UI</summary>

 ```java
package stepDefinitions;

import cucumber.TestContext;
import io.cucumber.java.en.Given;
import io.cucumber.java.en.When;
import pageObjects.HomePage;

public class HomePageSteps {

    TestContext testContext;
    HomePage homePage;

    public HomePageSteps(TestContext context) {
        testContext = context;
        homePage = testContext.getPageObjectManager().getHomePage();
    }

    @Given("^user is on Home Page$")
    public void user_is_on_Home_Page(){
        homePage.navigateTo_HomePage();
    }

    @When("^he search for \"([^\"]*)\"$")
    public void he_search_for(String product)  {
        homePage.perform_Search(product);
    }

}
```

 ```java
package stepDefinitions;

import cucumber.TestContext;
import io.cucumber.java.en.When;
import enums.Context;
import pageObjects.ProductListingPage;

public class ProductPageSteps {

    TestContext testContext;
    ProductListingPage productListingPage;

    public ProductPageSteps(TestContext context) {
        testContext = context;
        productListingPage = testContext.getPageObjectManager().getProductListingPage();
    }

    @When("^choose to buy the first item$")
    public void choose_to_buy_the_first_item() {
        String productName = productListingPage.getProductName(0);
        testContext.scenarioContext.setContext(Context.PRODUCT_NAME, productName);

        productListingPage.select_Product(0);
        productListingPage.clickOn_AddToCart();
    }
}
```

 ```java
package stepDefinitions;

import cucumber.TestContext;
import io.cucumber.java.en.When;
import pageObjects.CartPage;

public class CartPageSteps {

    TestContext testContext;
    CartPage cartPage;

    public CartPageSteps(TestContext context) {
        testContext = context;
        cartPage = testContext.getPageObjectManager().getCartPage();
    }

    @When("^moves to checkout from mini cart$")
    public void moves_to_checkout_from_mini_cart(){
        cartPage.clickOn_Cart();
        cartPage.clickOn_ContinueToCheckout();
    }

}
```

```java
package stepDefinitions;

import cucumber.TestContext;
import io.cucumber.java.en.When;
import managers.FileReaderManager;
import pageObjects.CheckoutPage;
import testDataTypes.Customer;

public class CheckoutPageSteps {
    TestContext testContext;
    CheckoutPage checkoutPage;

    public CheckoutPageSteps(TestContext context) {
        testContext = context;
        checkoutPage = testContext.getPageObjectManager().getCheckoutPage();
    }

    @When("^enter \\\"(.*)\\\" personal details on checkout page$")
    public void enter_personal_details_on_checkout_page(String customerName){
        Customer customer = FileReaderManager.getInstance().getJsonReader().getCustomerByName(customerName);
        checkoutPage.fill_PersonalDetails(customer);
    }

    @When("^select same delivery address$")
    public void select_same_delivery_address(){
        checkoutPage.check_ShipToDifferentAddress(false);
    }

    @When("^select payment method as \"([^\"]*)\" payment$")
    public void select_payment_method_as_payment(String arg1){
        checkoutPage.select_PaymentMethod("CheckPayment");
    }

    @When("^place the order$")
    public void place_the_order() {
        checkoutPage.check_TermsAndCondition(true);
        checkoutPage.clickOn_PlaceOrder();
    }

}
```

</details>

<details>
<summary>API</summary>

 ```java
package stepDefinitions;

import apiEngine.EndPoints;
import cucumber.ScenarioContext;
import cucumber.TestContext;

public class BaseSteps {

    private EndPoints endPoints;
    private ScenarioContext scenarioContext;

    public BaseSteps(TestContext testContext) {
        endPoints = testContext.getEndPoints();
        scenarioContext = testContext.getScenarioContext();
    }

    public EndPoints getEndPoints() {
        return endPoints;
    }

    public ScenarioContext getScenarioContext() {
        return scenarioContext;
    }
}
```

 ```java
package stepDefinitions;

import apiEngine.EndPoints;
import apiEngine.model.requests.*;
import cucumber.TestContext;
import io.cucumber.java.en.Given;

public class AccountSteps extends BaseSteps {

    public AccountSteps(TestContext testContext){
        super(testContext);
    }

    @Given("^I am an authorized user$")
    public void iAmAnAuthorizedUser() {
        AuthorizationRequest authRequest = new AuthorizationRequest("demoqa", "Test@123");
        getEndPoints().authenticateUser(authRequest);
    }
}
```

 ```java
package stepDefinitions;

import apiEngine.EndPoints;
import apiEngine.IRestResponse;
import apiEngine.model.*;
import apiEngine.model.requests.*;
import apiEngine.model.responses.*;
import cucumber.TestContext;
import io.cucumber.java.en.Given;
import io.cucumber.java.en.Then;
import io.cucumber.java.en.When;
import io.restassured.response.Response;

import static org.junit.jupiter.api.Assertions.*;

public class BooksSteps  extends BaseSteps {

    public BooksSteps(TestContext testContext){
        super(testContext);
    }

    private final String USER_ID = "79617b93-711a-4b7d-a62f-9c7563a1b3d2";
    private Response response;
    private IRestResponse<UserAccount> userAccountResponse;
    private Book book;

    @Given("^A list of books are available$")
    public void listOfBooksAreAvailable() {
        IRestResponse<Books> booksResponse = getEndPoints().getBooks();
        book = booksResponse.getBody().books.get(0);
    }

    @When("^I add a book to my reading list$")
    public void addBookInList() {

        ISBN isbn = new ISBN(book.isbn);
        AddBooksRequest addBooksRequest = new AddBooksRequest(USER_ID, isbn);
        userAccountResponse = getEndPoints().addBook(addBooksRequest);
    }

    @Then("^The book is added$")
    public void bookIsAdded() {

        assertTrue(userAccountResponse.isSuccessful());
        assertEquals(201, userAccountResponse.getStatusCode());

        assertEquals(USER_ID, userAccountResponse.getBody().userID);
        assertEquals(book.isbn, userAccountResponse.getBody().books.get(0).isbn);
    }

    @When("^I remove a book from my reading list$")
    public void removeBookFromList() {

        RemoveBookRequest removeBookRequest = new RemoveBookRequest(USER_ID, book.isbn);
        response = getEndPoints().removeBook(removeBookRequest);
    }

    @Then("^The book is removed$")
    public void bookIsRemoved() {

        assertEquals(204, response.getStatusCode());

        userAccountResponse = getEndPoints().getUserAccount(USER_ID);
        assertEquals(200, userAccountResponse.getStatusCode());

        assertEquals(0, userAccountResponse.getBody().books.size());
    }

}

```

</details>
