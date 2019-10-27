# From Scripting to Framework course on TestAutomationU.com

## Chapter 1 - Machine Setup

1. Requirements
    - .NET Core version 2.2 (latest at time of recording) or greater
    - VS Code

2. Make a new project called `scripting-to-framework` and open it in VS Code

3. Install Extensions in VS Code
    - C# by Microsoft
    - PackSharp by Carlos Kidman

4. Open the Command Palette for each of these commands
    - `PackSharp: Create New Project` > select `Class Library` > call the Project "Framework"
    - `PackSharp: Create New Project` > select `Class Library` > call the Project "Royale"
    - `PackSharp: Create New Project` > select `NUnit 3 Test Project` > call the Project "Royale.Tests"
    - `PackSharp: Create New Project` > select `Solution File` > call the Project "StatsRoyale"

> NOTE: The Solution (.sln) file will manage the Project (.csproj) files while the Project files handle their own packages and dependencies. As you add things, this is all handled for you! Very cool.

5. Add the Projects to the Solution. Run these commands in the Terminal:
    - `$ dotnet sln add Framework`
    - `$ dotnet sln add Royale`
    - `$ dotnet sln add Royale.Tests`

6. Build the Solution so we know everything is working
    - `$ dotnet build`

7. Open the `UnitTest1.cs` file in the Royale.Tests project
    - C# will "spin up" so you can start coding in C# and get helpful completions, hints, etc.
    - VS Code will ask if you want "Required assets to build and debug". Add them by clicking the "Yes" button.
        - If you do not get this immediately, try closing VS Code and reopening the project.
        - This will add a `.vscode` folder to your solution, but this is required to run and debug the tests.

8. Run the Tests
    - `$ dotnet test`
    - This will run all the tests, but you only have one right now. It should pass.


## Chapter 2 - Script Some Tests

> NOTE: Our application under test (website) is https://statsroyale.com

1. Change the name of the Test Class and Test Method so they make more sense
    - Test Class from `Tests` to `CardTests`
    - File name from `Tests.cs` to `CardTests.cs`
    - Test Method from `Test1()` to `Ice_Spirit_is_on_Cards_Page()`

2. Install Selenium NuGet (package) with PackSharp
    - Open Command Palette > select `PackSharp: Bootstrap Selenium` > add to `Royale.Tests`, our Test Project
    - If you open the `Royale.Tests.csproj` file, you will see that Selenium packages have been added
    - You will also see a `_drivers` directory is added at the Workspace root

> NOTE: This command installs the **latest** versions of chromedriver and the Selenium packages.

3. Use Selenium in our CardTests.cs file
    - Within the CardTests class, add the `IWebDriver driver;` field to the top
    - Resolve the error by hovering the red line and click on the lightbulb
        - The first option will want you to add the `using OpenQA.Selenium;` statement. Select that one
        - The error will go away and you will see that using statement is added automatically

4. SetUp and TearDown methods
    - The [SetUp] method is run *before each* test. Change the method name from `Setup()` to `BeforeEach()`
    - Add a [TearDown] method which runs *after each* test. Call this method `AfterEach()`

5. Within AfterEach(), add:
    - `driver.Quit();`
    - This will close the driver after each test is finished

6. Within BeforeEach(), add:
    - `driver = new ChromeDriver(<path-to-chromedriver>);`
    - This will open a new Chrome browser for every test
    - "Point" the ChromeDriver() to wherever you store your `chromedriver` or `chromedriver.exe`.

> NOTE: Everyone manages their drivers (like `chromedriver`, `geckodriver`, etc.) differently. Use your preferred method.

7. Write the first test. The steps are:
    1. Go to https://statsroyale.com
    2. Click on Cards link
    3. Assert Ice Spirit is displayed

8. For the second test, the steps are:
    1. Go to https://statsroyale.com
    2. Click on Cards link
    3. Click on Ice Spirit card
    4. Assert the basic headers are correct. These headers are:
        - Name ("Ice Spirit")
        - Type ("Troop")
        - Arena ("Arena 8")
        - Rarity ("Common")

9. There's a lot of code in this one, so make sure to pause and replay as much as you need :)


## Chapter 3 - Page Object Model

Follow the video to for an explanation on the `Page Object Model` and `Page Map Pattern`.

1. Within the Royale project, create a `Pages` directory. This is where all of our Page objects will live.

2. Move the `Class1.cs` file into `Pages` and rename it to `HeaderNav.cs`

3. Within the file, rename `Class1` to `HeaderNav` and then make another class called `HeaderNavMap`

4. Use PackSharp to restructure our packages and dependencies so we leverage Framework and Royale projects
    1. Move Selenium to the `Framework` project
        - Open Command Palette > `PackSharp: Bootstrap Selenium` > select `Framework`
    2. Remove Selenium from `Royale.Tests` project
        - Open Command Palette > `PackSharp: Remove Package` > select `Royale.Tests` > select `Selenium.Support`
        - Also remove `Selenium.WebDriver`

5. Framework is our base, so we want the projects to reference each other in a linear way.

    Framework -> Royale -> Royale.Tests

    `Royale.Tests` will reference `Royale` which references `Framework`

    - Open Command Palette > `PackSharp: Add Project Reference` > select `Royale.Tests` > select `Royale`
    - Open Command Palette > `PackSharp: Add Project Reference` > select `Royale` > select `Framework`

6. Now we can bring in `using OpenQA.Selenium` in our `HeaderNav.cs` file

> NOTE: The rest of this video is very "code-heavy", so make sure to follow along there

7. The naming convention for Pages and Page Maps is very simple. If you have a Home page, then you would do this:
    - Page => `HomePage`
    - Map => `HomePageMap`

8. In the video, there is a "jump" from `11:22` to `11:25` where I am able to use the `Map.Card()` immediately. At this point, you will have an error. All you need to do is:
    - Add the `public readonly CardPageMap Map;` field at the top of the `CardsPage` class and the Constructor as well
    - You will see the needed code at `11:43`. Sorry about that!

9. Card Details Page and Map
    - Take a moment to pause the video and copy the code to move forward

10. At the end of the video, run your second test. It should fail! Your challenge is to solve this error so the test passes.


## Chapter 4 - Models and Services

1. Copy and Paste the second test to make a third test. However, this test will be for the "Mirror" card, so change the values in the test accordingly.

2. In the Framework project, make a new folder called `Models` and move the `Class1.cs` file into it.

3. Rename the file to `MirrorCard.cs` and change the class name too

4. We'll be adding the card properties that we care about and give them default values.

> NOTE: Some portions are sped up, so just pause the video when it gets back to regular speed and copy as needed.

5. Because `MirrorCard` and `IceSpiritCard` share the same properties, we can create a "base" `Card` with these properties and have our cards inherit it. This is very similar to how our `PageBase` class works. Every page on the website has access to the `HeaderNav`, right? Instead of repeating the header navigation bar on every page, we can create it once and then share it to every page! This helps us avoid repeating ourselves as well as simplifying our code.

    - Copy and Paste the `MirrorCard` into a new file called `IceSpiritCard.cs` in Models.
    - Change the default values to the Ice Spirit values

6. Now we can bring this all together by creating a `GetBaseCard()` method in our `CardDetailsPage`. Pause the video as needed.

7. We can use this method in the Mirror test by getting two cards:
    - `var card = cardDetails.GetBaseCard();`
        - This card is the one we get off of the page using Selenium and our Page objects
    - `var mirror = new MirrorCard();`
        - This card has the actual values that we expect our Mirror Card to have

8. Assert that the expected values from our `var mirror` card match the actual values we received from `var card`.

9. Our first Card Services

    These "services" are abstractions of how we end up getting cards from a data store.

    The "In Memory" card service will help with getting cards from local, hard-coded values we've specified in our Models directory, but ultimately we'd like to get these values from actual data stores like a database. We will be doing that in a future chapter :)

10. Now that the Services are complete and being used in the test, our 2nd and 3rd tests are almost identical. The only difference are the names!

    - We can now leverage the `[TestCase]` and `[TestSource]` attributes from the `NUnit Test Framework`
    - These will "feed" values into the test
    - This turns a single Test Method into multiple Test Cases!

11. We can use the `[Parallelizable]` attribute to make these Test Cases run in parallel!
    - `[Parallelizable(ParallelScope.Children)]`

12. Run the 3rd test. It will spin up two browsers at the same time, but most likely both with fail. What happened?
    - You will also notice that one of the two browsers doesn't close even after failing. Can you guess why?
    - The answer is that we are instantiating a single WebDriver for both tests, so they are fighting each other over the driver!
    - We will solve this in the next chapter

13. Last thing we'll do is add the `[Category]` attribute to our test
    - `[Category("cards")]`
    - We can use Categories when running our tests. To run only the tests with the Category of "cards", we would do:
        - `$ dotnet test --filter testcategory=cards`


## Chapter 5 - Customizing WebDriver

1. Start by creating a `Selenium` folder in our `Framework` project

2. Create a `Driver.cs` file in `Selenium`. This is where our wrapper of WebDriver will exist.

3. The key to achieving the simplicity of a `static` even though it's not a singleton is with the `[ThreadStatic]` attribute.
    ```c#
    [ThreadStatic]
    private static IWebDriver _driver;

    public static void Init()
    {
        _driver = new ChromeDriver();
    }

    public static IWebDriver Current => _driver ?? throw new System.ArgumentException("_driver is null.");
    ```

    - This is the bread and butter of this approach. `_driver` is the instance of WebDriver on a thread.
    - `Current` is how you access the current instance of WebDriver for the test you're on

4. In the `BeforeEach()` method of CardTests, you can now use `Driver.Init();` and remove the "global" `IWebDriver driver` at the top.

5. Then create a "Pages Wrapper"
    1. Create `Pages.cs` file in `Royale.Pages` directory
    2. Create a field for each page we have
        ```c#
        [ThreadStatic]
        public static CardsPage Cards;

        [ThreadStatic]
        public static CardDetailsPage CardDetails;

        public static void Init()
        {
            Cards = new CardsPage(Driver.Current);
            CardDetails = new CardDetailsPage(Driver.Current);
        }
        ```

6. In the tests, we can get rid of any lines that say `new Page()` because our Pages Wrapper handles that for us
    - Replace: `var cardsPage = new CardsPage(Driver.Current);`
    - With: `Pages.Cards`

7. In our `Driver` class, let's add a way to navigate to a URL so we don't have to say `Driver.Current.Url`
    ```c#
    public static void Goto(string url)
    {
        if (!url.StartsWith("http"))
        {
            url = $"http://{url}";
        }

        Debug.WriteLine(url);
        Current.Navigate().GoToUrl(url);
    }
    ```

8. Your `BeforeEach()` method should now look like this:
    ```c#
    [SetUp]
    public void BeforeEach()
    {
        Driver.Init();
        Pages.Init();
        Driver.Goto("https://statsroyale.com");
    }
    ```

9. Now add a `FindElement()` and `FindElements()` method to Driver.

10. Go to each of the Page Objects and replace `_driver` with `Driver`.
    - We no longer need the `IWebDriver _driver` field in the Page Maps
    - We no longer need the constructors in the Page Maps
    - We no longer need to pass in `IWebDriver driver` in Page Object constructors!

11. Yes, a lot of code is deleted, but that's a good thing!

12. The challenge is to add a `Quit()` method to our Driver class to get rid of `Driver.Current.Quit();` in the `AfterEach()`.


## Chapter 6 - Test Data from an API

1. From Postman, we saw the the /cards endpoint returned all of the cards that exist in the game. However, they had a different shape than our Card class. Update our Card class to include these new properties:

    ```c#
    public class Card
    {
        public virtual string Id { get; set; }

        public virtual string Name { get; set; }

        public virtual string Icon { get; set; }

        public virtual int Cost { get; set; }

        public virtual string Rarity { get; set; }

        public virtual string Type { get; set; }

        public virtual string Arena { get; set; }
    }
    ```

2. We want to leverage that endpoint by creating an ApiCardService
    - Create `ApiCardService.cs` within `Framework.Services`
    - Implement the `ICardService` interface

3. Install the `Newtonsoft.Json` package to the `Framework` project
    - Open Command Palette > `PackSharp: Add Package` > search `Newtonsoft` > select `Newtonsoft.Json`

4. Install the `RestSharp` package to the `Framework` project
    - Open Command Palette > `PackSharp: Add Package` > search `RestSharp` > select `RestSharp`

5. Within the `ApiCardService` class, we will use RestSharp to make the call just like PostMan. We'll call this method `GetAllCards()`

    ```c#
    public const string CARDS_API = "https://statsroyale.com/api/cards";

    public IList<Card> GetAllCards()
    {
        var client = new RestClient(CARDS_API);
        var request = new RestRequest
        {
            Method = Method.GET,
            RequestFormat = DataFormat.Json
        };

        var response = client.Execute(request);

        if (response.StatusCode != System.Net.HttpStatusCode.OK)
        {
            throw new System.Exception("/cards endpoint failed with " + response.StatusCode);
        }

        return JsonConvert.DeserializeObject<IList<Card>>(response.Content);
    }
    ```

6. Now let's implement the `GetCardByName(string cardName)` method

    ```c#
    public Card GetCardByName(string cardName)
    {
        var cards = GetAllCards();
        return cards.FirstOrDefault(card => card.Name == cardName);
    }
    ```

7. Now we can use the list of cards from the API rather than our hard-coded list of two cards. In CardTests, put this above our first test:
    ```c#
    static IList<Card> apiCards = new ApiCardService().GetAllCards();
    ```

8. Our tests can now leverage this list of cards using the `[TestCaseSource]` attribute. Our first test should then look like:

    ```c#
    [Test, Category("cards")]
    [TestCaseSource("apiCards")]
    [Parallelizable(ParallelScope.Children)]
    public void Card_is_on_Cards_Page(Card card)
    {
        var cardOnPage = Pages.Cards.Goto().GetCardByName(card.Name);
        Assert.That(cardOnPage.Displayed);
    }
    ```
    - We have added the "cards" Category to the test
    - The TestCaseSource is now coming from "apiCards"
    - We've added the same [Parallelizable] attribute to this test
    - Changed the test name to better reflect that it tests all cards and not just Ice Spirit anymore
    - The object we are passing into the test is not a `string`, but a `Card` object.

9. Update the second test to also use `apiCards`

10. Run the tests but use the `NUnit.NumberOfTestWorkers=2` argument so you don't overload your machine
    ```bash
    $ dotnet test --filter testcategory=cards -- NUnit.NumberOfTestWorkers=2
    ```
    - This will only run two tests at a time which is probably best for your machine
    - You can press `CTRL + C` in the terminal to cancel the test execution

11. After some test failures, you will see some interesting errors. The biggest one is that "Troop" is not equal to "tid_card_type_character" and "Spell" is not equal to "tid_card_type_spell".

The challenge is to solve this error without touching code in the CardTests.cs file.

> HINT: `tid_card_type_spell` already has the word "spell" in it. Could we use that somehow?
> HINT: `Troop` and `character` are the same thing in the context of the game. We should treat characters as troops and vice-versa
