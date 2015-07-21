---
layout: post
title: "The ePages Selenium Framework"
date: "2015-07-21 12:40:00"
icon: ship
tags: selenium, test, continuous delivery
categories: tech-stories
authors: ["Jan M.", "Martin K.", "Kay A."]
---

With the help of Selenium we create and run automated test scripts for
the user interface of ePages. In order to make it quick and easy for
the developers to write these tests we have built the ePages
Selenium Framework.

# Browser automation with Selenium

Selenium is an open source project which provides a couple of tools
and libraries for web browser automation. Its sub-projects are
Selenium WebDriver, Selenium Grid, Selenium IDE and Selenium RC.
Selenium WebDriver implements the W3C WebDriver specification draft
which describes an API for the interaction with different web
browsers, such as Chrome or Firefox. Selenium Grid supports the
execution of tests scripts in a network of remote test nodes.
The Selenium IDE is a Firefox plug-in which provides a capture
and replay function. Selenium Remote Control is the predecessor
of Selenium Grid.

The most important part for us is the Selenium WebDriver. In order
to get a basic idea of how it is working, you can have a look on the
following simplified test script.

{% highlight text %}
[...]

WebDriver driver = new FirefoxDriver();

driver.get("https://github.com/");

WebElement searchInputField = driver.findElement(By.name("q"));
searchInputField.sendKeys("epages");
searchInputField.sendKeys(Keys.RETURN);

WebElement docsRepo = driver.findElement(By.cssSelector("h3.repo-list-name a"));
String firstRepo = docsRepo.getAttribute("href");
assertEquals("https://github.com/ePages-de/epages-docs", firstRepo);

[...]
{% endhighlight %}

Before the execution of the test we need to start a browser,
in this case Mozilla Firefox. The next step in the test script
is to go to the website we want to test. Then we create an object
for the web element to be interacted with. To submit a search
query we enter "epages" and press the return key. Afterwards
we are retrieving the relevant information from the website
and compare it with the expected test data.

If you would like to understand how Selenium WebDriver is working
internally you can read a description of its architecture here.

# Basic concepts of the ESF

In order to make it as easy as possible for our developers to write
automated UI tests, and in order to avoid duplicated code from
developing in different teams, we have built a framework around
Selenium: the ePages Selenium Framework or just ESF.

The ESF is a Java project build upon Selenium WebDriver
and the test framework TestNG. It uses Gradle as build tool and
supports starting tests from within an IDE (e.g. Eclipse) and from
the command-line. Browser start and test report generation is
controlled with the help of TestNG configuration methods and event listeners. 

The actual test suite consists of test scripts and Page Objects.
Page Objects are abstractions of individual web pages. They provide
service methods that enable a user to interact with the
underlying web page. These service methods encapsulate the
locators for the web elements and all of the logic to gather
information from the page or interacting with it. The Tests
are then using these service methods to fulfill defined
test specifications. The following picture shows this structure.

{% image esf-concept.png %}{% endimage %}

Here you can see a short test script for the search functionality.
The test scripts are always derived from the base class TestBaseEp6.
That way the developer can focus on writing the test method, leaving
all the configuration and setup work to the framework. One of the
first steps of each test script is to
get an instance of the ActionBot. This class provides access to
the WebDriver and additional utility methods like navigating through
the ePages shop. This particular test will go to an ePages storefront,
search for a product with the name "Mag-Lite Mini" and check if the
product is part of the search result.

{% highlight text %}
[...]

public final class SearchTest extends TestBaseEp6 {

    @Test(groups = { "SEARCH", "CORE” })
    public void simpleSearchTest() {

        final String productName = "Mag-Lite Mini";

        final ActionBotEp6 actionBot = this.getActionBot();
        final PageHomepage sfLanding = actionBot.goToStorefront();
        final PageSearchResults searchResults = sfLanding.searchFor(productName);

        assertTrue("The product we searched for is part of the search result.",
                searchResults.isProductPartOfSearchResult(productName));
    }
}
{% endhighlight %}

# Role in the release process

The ESF plays an important role in the ePages release process.
As we are releasing a new ePages version every two weeks, it is impossible to
do manual regression testing for all our features, more than fifty technology partners,
various installation types
and support for different Linux distributions. Beside a lot of other testing, like unit and
integration tests, the ESF tests are executed up to two times a day
in our Continuous Delivery Pipeline.

Our test suite consists of more than one hundred test scripts.
They cover our most important features and there is a basic test
for almost all payment and shipping integrations. Additionally
the Consulting team is running their own test suite for provider specific features
before Application Management roles out a new ePages version to our customers.


