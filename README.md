## **Purpose**

The purpose of this project is to crawl our client's storefront website and parse all the metadata displayed as if it was a user (typical customer searching for a TV show or movie).

## **Design**

This application is written in Java. It starts by displaying a static HTML page which lets you select which storefront and territory to crawl. Once you hit 'launch', the application will call method 'beginWork', which will configure the run settings and determine if it was launched in full automation (task mode) or locally (manual run). Each crawler is individually implemented, but derives from the abstract class AbstractCrawlerWorker. Each storefront website is more or less the same design, as in: you search for the movie/tv show that you want, you click on the correct result from the search results, and you see all the metadata associated with the item.

Data collection is gathered by **ChromeDriver** (application) and **Selenium WebDriver** (API). ChromeDriver is a separate executable that Selenium WebDriver uses to control Chrome (the web browser). Selenium is actually pretty simple to use: webElement.find(<elements in HTML>), interact with them (via a click or send key input), and then read/store the text/image. Some of our crawlers parse through the JSON response from a website, and others simply read directly from the HTML. You can find more useful information on how to use Selenium here: [http://www.seleniumhq.org/docs/03\_webdriver.jsp](http://www.seleniumhq.org/docs/03_webdriver.jsp) and here: [http://toolsqa.com/selenium-tutorial/](http://toolsqa.com/selenium-tutorial/)

When we crawl websites, we aren't looking to get the fastest run time possible. In fact, we are emulating the slowest part of the computer, the human input. We want to emulate a real human using the storefront service to:

    A. Get the same results a regular customer would get.

    B. Not be detected by the website as a robot and have to solve captchas.

We use the **Helpers** and **CommonHelpers** classes to add waits and random pauses between inputs to simulate real human interaction on the sites.

Some storefronts require special treatment, as some will only show prices and other metadata when you are either logged into a user account, under a local IP, or both.

The **storm\_services** project hosts the STORM website. Similar to the webcrawler, it reads and writes data to the production database. We have recently refactored our code so that methods found in both storm\_services and storm\_webgatherer are placed in the project **mem\_common**. If you want to add a new method or class to both projects, go to mem\_common's gradle and run artifactoryPublish, and then refresh your gradle in the project you are working on.

The crawler will use a location specific proxy IP for each territory (when it's required by the storefront).

## **Automation**

Automation runs and manual runs are different. Manual runs are run either in the IDE or by executing the compiled Jar with the command java -jar storm\_webgatherer-1.0-SNAPSHOT.jar. Automation runs work differently: they are scheduled in the AWS console to run at a certain time everyday, and use a longer command to launch, which will launch them in --task mode, creating threads of the crawler.

## **Random Notes**

1. We only deal with streaming/digital purchase options (so no DVD's or other physical type).
2. We use chrome for all our crawlers. Our program will use Firefox only if there is a problem with the chromedriver setup (If there is a problem, you have probably not set the path correctly in your app.json).
3. Our crawlers run on Amazon's EC2 servers on Ubuntu.
4. We are searching over 20,000 titles with our crawlers!
5. Sony (Playstation) and iTunes both use an API to crawl data. ChromeDriver will remain blank when manually run.
