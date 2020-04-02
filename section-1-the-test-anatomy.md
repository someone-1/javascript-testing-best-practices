# Section 1: The Test Anatomy

<br/>

## ⚪ ️ 1.1 Include 3 parts in each test name

:white_check_mark: **Do:** A test report should tell whether the current application revision satisfies the requirements for the people who are not necessarily familiar with the code: the tester, the DevOps engineer who is deploying and the future you two years from now. This can be achieved best if the tests speak at the requirements level and include 3 parts:

(1) What is being tested? For example, the ProductsService.addNewProduct method

(2) Under what circumstances and scenario? For example, no price is passed to the method

(3) What is the expected result? For example, the new product is not approved

<br/>

❌ **Otherwise:** A deployment just failed, a test named “Add product” failed. Does this tell you what exactly is malfunctioning?

<br/>

**👇 Note:** Each bullet has code examples and sometime also an image illustration. Click to expand

<details><summary>✏ <b>Code Examples</b></summary>
  
<br/>
  
### :clap: Doing It Right Example: A test name that constitutes 3 parts

![](https://img.shields.io/badge/🔨%20Example%20using%20Mocha-blue.svg "Using Mocha to illustrate the idea")

```javascript
//1. unit under test
describe('Products Service', function() {
  describe('Add new product', function() {
    //2. scenario and 3. expectation
    it('When no price is specified, then the product status is pending approval', ()=> {
      const newProduct = new ProductService().add(...);
      expect(newProduct.status).to.equal('pendingApproval');
    });
  });
});

```

<br/>

### :clap: Doing It Right Example: A test name that constitutes 3 parts

![alt text](/assets/bp-1-3-parts.jpeg "A test name that constitutes 3 parts")

</details>

<br/><br/>

## ⚪ ️ 1.2 Structure tests by the AAA pattern

:white_check_mark: **Do:** Structure your tests with 3 well-separated sections Arrange, Act & Assert (AAA). Following this structure guarantees that the reader spends no brain-CPU on understanding the test plan:

1st A - Arrange: All the setup code to bring the system to the scenario the test aims to simulate. This might include instantiating the unit under test constructor, adding DB records, mocking/stubbing on objects and any other preparation code

2nd A - Act: Execute the unit under test. Usually 1 line of code

3rd A - Assert: Ensure that the received value satisfies the expectation. Usually 1 line of code

<br/>

❌ **Otherwise:** Not only do you spend hours understanding the main code, but what should have been the simplest part of the day (testing) stretches your brain

<br/>

<details><summary>✏ <b>Code Examples</b></summary>

<br/>

### :clap: Doing It Right Example: A test structured with the AAA pattern

![](https://img.shields.io/badge/🔧%20Example%20using%20Jest-blue.svg "Examples with Jest") ![](https://img.shields.io/badge/🔧%20Example%20using%20Mocha-blue.svg "Examples with Mocha")

```javascript
describe("Customer classifier", () => {
  test("When customer spent more than 500$, should be classified as premium", () => {
    //Arrange
    const customerToClassify = { spent: 505, joined: new Date(), id: 1 };
    const DBStub = sinon.stub(dataAccess, "getCustomer").reply({ id: 1, classification: "regular" });

    //Act
    const receivedClassification = customerClassifier.classifyCustomer(customerToClassify);

    //Assert
    expect(receivedClassification).toMatch("premium");
  });
});
```

<br/>

### :thumbsdown: Anti Pattern Example: No separation, one bulk, harder to interpret

```javascript
test("Should be classified as premium", () => {
  const customerToClassify = { spent: 505, joined: new Date(), id: 1 };
  const DBStub = sinon.stub(dataAccess, "getCustomer").reply({ id: 1, classification: "regular" });
  const receivedClassification = customerClassifier.classifyCustomer(customerToClassify);
  expect(receivedClassification).toMatch("premium");
});
```

</details>

<br/><br/>

## ⚪ ️1.3 Describe expectations in a product language: use BDD-style assertions

:white_check_mark: **Do:** Coding your tests in a declarative-style allows the reader to get the grab instantly without spending even a single brain-CPU cycle. When you write imperative code that is packed with conditional logic, the reader is forced to exert more brain-CPU cycles. In that case, code the expectation in a human-like language, declarative BDD style using `expect` or `should` and not using custom code. If Chai & Jest doesn't include the desired assertion and it’s highly repeatable, consider [extending Jest matcher (Jest)](https://jestjs.io/docs/en/expect#expectextendmatchers) or writing a [custom Chai plugin](https://www.chaijs.com/guide/plugins/)
<br/>

❌ **Otherwise:** The team will write less tests and decorate the annoying ones with .skip()

<br/>

<details><summary>✏ <b>Code Examples</b></summary><br/>

![](https://img.shields.io/badge/🔧%20Example%20using%20Mocha-blue.svg "Examples with Mocha & Chai") ![](https://img.shields.io/badge/🔧%20Example%20using%20Jest-blue.svg "Examples with Jest")

### :thumbsdown: Anti Pattern Example: The reader must skim through not so short, and imperative code just to get the test story

```javascript
test("When asking for an admin, ensure only ordered admins in results", () => {
  //assuming we've added here two admins "admin1", "admin2" and "user1"
  const allAdmins = getUsers({ adminOnly: true });

  let admin1Found,
    adming2Found = false;

  allAdmins.forEach(aSingleUser => {
    if (aSingleUser === "user1") {
      assert.notEqual(aSingleUser, "user1", "A user was found and not admin");
    }
    if (aSingleUser === "admin1") {
      admin1Found = true;
    }
    if (aSingleUser === "admin2") {
      admin2Found = true;
    }
  });

  if (!admin1Found || !admin2Found) {
    throw new Error("Not all admins were returned");
  }
});
```

<br/>

### :clap: Doing It Right Example: Skimming through the following declarative test is a breeze

```javascript
it("When asking for an admin, ensure only ordered admins in results", () => {
  //assuming we've added here two admins
  const allAdmins = getUsers({ adminOnly: true });

  expect(allAdmins)
    .to.include.ordered.members(["admin1", "admin2"])
    .but.not.include.ordered.members(["user1"]);
});
```

</details>

<br/><br/>

## ⚪ ️ 1.4 Stick to black-box testing: Test only public methods

:white_check_mark: **Do:** Testing the internals brings huge overhead for almost nothing. If your code/API delivers the right results, should you really invest your next 3 hours in testing HOW it worked internally and then maintain these fragile tests? Whenever a public behavior is checked, the private implementation is also implicitly tested and your tests will break only if there is a certain problem (e.g. wrong output). This approach is also referred to as `behavioral testing`. On the other side, should you test the internals (white box approach) — your focus shifts from planning the component outcome to nitty-gritty details and your test might break because of minor code refactors although the results are fine - this dramatically increases the maintenance burden
<br/>

❌ **Otherwise:** Your tests behave like the [boy who cried wolf](https://en.wikipedia.org/wiki/The_Boy_Who_Cried_Wolf): shouting false-positive cries (e.g., A test fails because a private variable name was changed). Unsurprisingly, people will soon start to ignore the CI notifications until someday, a real bug gets ignored…

<br/>
<details><summary>✏ <b>Code Examples</b></summary>

<br/>

### :thumbsdown: Anti Pattern Example: A test case is testing the internals for no good reason

![](https://img.shields.io/badge/🔧%20Example%20using%20Mocha-blue.svg "Examples with Mocha & Chai")

```javascript
class ProductService {
  //this method is only used internally
  //Change this name will make the tests fail
  calculateVATAdd(priceWithoutVAT) {
    return { finalPrice: priceWithoutVAT * 1.2 };
    //Change the result format or key name above will make the tests fail
  }
  //public method
  getPrice(productId) {
    const desiredProduct = DB.getProduct(productId);
    finalPrice = this.calculateVATAdd(desiredProduct.price).finalPrice;
    return finalPrice;
  }
}

it("White-box test: When the internal methods get 0 vat, it return 0 response", async () => {
  //There's no requirement to allow users to calculate the VAT, only show the final price. Nevertheless we falsely insist here to test the class internals
  expect(new ProductService().calculateVATAdd(0).finalPrice).to.equal(0);
});
```

</details>

<br/><br/>

## ⚪ ️ ️1.5 Choose the right test doubles: Avoid mocks in favor of stubs and spies

:white_check_mark: **Do:** Test doubles are a necessary evil because they are coupled to the application internals, yet some provide immense value (<a href="https://martinfowler.com/articles/mocksArentStubs.html" data-href="https://martinfowler.com/articles/mocksArentStubs.html" class="markup--anchor markup--p-anchor" rel="noopener nofollow" target="_blank">[Read here a reminder about test doubles: mocks vs stubs vs spies](https://martinfowler.com/articles/mocksArentStubs.html)</a>).

Before using test doubles, ask a very simple question: Do I use it to test functionality that appears, or could appear, in the requirements document? If no, it’s a white-box testing smell.

For example, if you want to test that your app behaves reasonably when the payment service is down, you might stub the payment service and trigger some ‘No Response’ return to ensure that the unit under test returns the right value. This checks our application behavior/response/outcome under certain scenarios. You might also use a spy to assert that an email was sent when that service is down — this is again a behavioral check which is likely to appear in a requirements doc (“Send an email if payment couldn’t be saved”). On the flip side, if you mock the Payment service and ensure that it was called with the right JavaScript types — then your test is focused on internal things that got nothing with the application functionality and are likely to change frequently
<br/>

❌ **Otherwise:** Any refactoring of code mandates searching for all the mocks in the code and updating accordingly. Tests become a burden rather than a helpful friend

<br/>

<details><summary>✏ <b>Code Examples</b></summary>

<br/>

### :thumbsdown: Anti-pattern example: Mocks focus on the internals

![](https://img.shields.io/badge/🔧%20Example%20using%20Sinon-blue.svg "Examples with Sinon")

```javascript
it("When a valid product is about to be deleted, ensure data access DAL was called once, with the right product and right config", async () => {
  //Assume we already added a product
  const dataAccessMock = sinon.mock(DAL);
  //hmmm BAD: testing the internals is actually our main goal here, not just a side-effect
  dataAccessMock
    .expects("deleteProduct")
    .once()
    .withArgs(DBConfig, theProductWeJustAdded, true, false);
  new ProductService().deletePrice(theProductWeJustAdded);
  dataAccessMock.verify();
});
```

<br/>

### :clap:Doing It Right Example: spies are focused on testing the requirements but as a side-effect are unavoidably touching to the internals

```javascript
it("When a valid product is about to be deleted, ensure an email is sent", async () => {
  //Assume we already added here a product
  const spy = sinon.spy(Emailer.prototype, "sendEmail");
  new ProductService().deletePrice(theProductWeJustAdded);
  //hmmm OK: we deal with internals? Yes, but as a side effect of testing the requirements (sending an email)
  expect(spy.calledOnce).to.be.true;
});
```

</details>

<br/><br/>

## 📗 Want to learn all these practices with live video?

### Visit my online course [Testing Node.js & JavaScript From A To Z](https://www.testjavascript.com)

<br/><br/>

## ⚪ ️1.6 Don’t “foo”, use realistic input data

:white_check_mark: **Do:** Often production bugs are revealed under some very specific and surprising input — the more realistic the test input is, the greater the chances are to catch bugs early. Use dedicated libraries like [Faker](https://www.npmjs.com/package/faker) to generate pseudo-real data that resembles the variety and form of production data. For example, such libraries can generate realistic phone numbers, usernames, credit card, company names, and even ‘lorem ipsum’ text. You may also create some tests (on top of unit tests, not as a replacement) that randomize fakers data to stretch your unit under test or even import real data from your production environment. Want to take it to the next level? See the next bullet (property-based testing).
<br/>

❌ **Otherwise:** All your development testing will falsely show green when you use synthetic inputs like “Foo”, but then production might turn red when a hacker passes-in a nasty string like “@3e2ddsf . ##’ 1 fdsfds . fds432 AAAA”

<br/>

<details><summary>✏ <b>Code Examples</b></summary>

<br/>

### :thumbsdown: Anti-Pattern Example: A test suite that passes due to non-realistic data

![](https://img.shields.io/badge/🔧%20Example%20using%20Jest-blue.svg "Examples with Jest")

```javascript
const addProduct = (name, price) => {
  const productNameRegexNoSpace = /^\S*$/; //no white-space allowed

  if (!productNameRegexNoSpace.test(name)) return false; //this path never reached due to dull input

  //some logic here
  return true;
};

test("Wrong: When adding new product with valid properties, get successful confirmation", async () => {
  //The string "Foo" which is used in all tests never triggers a false result
  const addProductResult = addProduct("Foo", 5);
  expect(addProductResult).toBe(true);
  //Positive-false: the operation succeeded because we never tried with long
  //product name including spaces
});
```

<br/>

### :clap:Doing It Right Example: Randomizing realistic input

```javascript
it("Better: When adding new valid product, get successful confirmation", async () => {
  const addProductResult = addProduct(faker.commerce.productName(), faker.random.number());
  //Generated random input: {'Sleek Cotton Computer',  85481}
  expect(addProductResult).to.be.true;
  //Test failed, the random input triggered some path we never planned for.
  //We discovered a bug early!
});
```

</details>

<br/><br/>

## ⚪ ️ 1.7 Test many input combinations using Property-based testing

:white_check_mark: **Do:** Typically we choose a few input samples for each test. Even when the input format resembles real-world data (see bullet ‘Don’t foo’), we cover only a few input combinations (method(‘’, true, 1), method(“string” , false” , 0)), However, in production, an API that is called with 5 parameters can be invoked with thousands of different permutations, one of them might render our process down ([see Fuzz Testing](https://en.wikipedia.org/wiki/Fuzzing)). What if you could write a single test that sends 1000 permutations of different inputs automatically and catches for which input our code fails to return the right response? Property-based testing is a technique that does exactly that: by sending all the possible input combinations to your unit under test it increases the serendipity of finding a bug. For example, given a method — addNewProduct(id, name, isDiscount) — the supporting libraries will call this method with many combinations of (number, string, boolean) like (1, “iPhone”, false), (2, “Galaxy”, true). You can run property-based testing using your favorite test runner (Mocha, Jest, etc) using libraries like [js-verify](https://github.com/jsverify/jsverify) or [testcheck](https://github.com/leebyron/testcheck-js) (much better documentation). Update: Nicolas Dubien suggests in the comments below to [checkout fast-check](https://github.com/dubzzz/fast-check#readme) which seems to offer some additional features and also to be actively maintained
<br/>

❌ **Otherwise:** Unconsciously, you choose the test inputs that cover only code paths that work well. Unfortunately, this decreases the efficiency of testing as a vehicle to expose bugs

<br/>

<details><summary>✏ <b>Code Examples</b></summary>

<br/>

### :clap: Doing It Right Example: Testing many input permutations with “fast-check”

![](https://img.shields.io/badge/🔧%20Example%20using%20Jest-blue.svg "Examples with Jest")

```javascript
import fc from "fast-check";

describe("Product service", () => {
  describe("Adding new", () => {
    //this will run 100 times with different random properties
    it("Add new product with random yet valid properties, always successful", () =>
      fc.assert(
        fc.property(fc.integer(), fc.string(), (id, name) => {
          expect(addNewProduct(id, name).status).toEqual("approved");
        })
      ));
  });
});
```

</details>

<br/><br/>

## ⚪ ️ 1.8 If needed, use only short & inline snapshots

:white_check_mark: **Do:** When there is a need for [snapshot testing](https://jestjs.io/docs/en/snapshot-testing), use only short and focused snapshots (i.e. 3-7 lines) that are included as part of the test ([Inline Snapshot](https://jestjs.io/docs/en/snapshot-testing#inline-snapshots)) and not within external files. Keeping this guideline will ensure your tests remain self-explanatory and less fragile.

On the other hand, ‘classic snapshots’ tutorials and tools encourage to store big files (e.g. component rendering markup, API JSON result) over some external medium and ensure each time when the test run to compare the received result with the saved version. This, for example, can implicitly couple our test to 1000 lines with 3000 data values that the test writer never read and reasoned about. Why is this wrong? By doing so, there are 1000 reasons for your test to fail - it’s enough for a single line to change for the snapshot to get invalid and this is likely to happen a lot. How frequently? for every space, comment or minor CSS/HTML change. Not only this, the test name wouldn’t give a clue about the failure as it just checks that 1000 lines didn’t change, also it encourages to the test writer to accept as the desired true a long document he couldn’t inspect and verify. All of these are symptoms of obscure and eager test that is not focused and aims to achieve too much

It’s worth noting that there are few cases where long & external snapshots are acceptable - when asserting on schema and not data (extracting out values and focusing on fields) or when the received document rarely changes
<br/>

❌ **Otherwise:** A UI test fails. The code seems right, the screen renders perfect pixels, what happened? your snapshot testing just found a difference from the origin document to current received one - a single space character was added to the markdown...

<br/>

<details><summary>✏ <b>Code Examples</b></summary>

<br/>

### :thumbsdown: Anti-Pattern Example: Coupling our test to unseen 2000 lines of code

![](https://img.shields.io/badge/🔧%20Example%20using%20Jest-blue.svg "Examples with Jest")

```javascript
it("TestJavaScript.com is renderd correctly", () => {
  //Arrange

  //Act
  const receivedPage = renderer
    .create(<DisplayPage page="http://www.testjavascript.com"> Test JavaScript </DisplayPage>)
    .toJSON();

  //Assert
  expect(receivedPage).toMatchSnapshot();
  //We now implicitly maintain a 2000 lines long document
  //every additional line break or comment - will break this test
});
```

<br/>

### :clap: Doing It Right Example: Expectations are visible and focused

```javascript
it("When visiting TestJavaScript.com home page, a menu is displayed", () => {
  //Arrange

  //Act
  const receivedPage = renderer
    .create(<DisplayPage page="http://www.testjavascript.com"> Test JavaScript </DisplayPage>)
    .toJSON();

  //Assert

  const menu = receivedPage.content.menu;
  expect(menu).toMatchInlineSnapshot(`
<ul>
<li>Home</li>
<li> About </li>
<li> Contact </li>
</ul>
`);
});
```

</details>

<br/><br/>

## ⚪ ️1.9 Avoid global test fixtures and seeds, add data per-test

:white_check_mark: **Do:** Going by the golden rule (bullet 0), each test should add and act on its own set of DB rows to prevent coupling and easily reason about the test flow. In reality, this is often violated by testers who seed the DB with data before running the tests ([also known as ‘test fixture’](https://en.wikipedia.org/wiki/Test_fixture)) for the sake of performance improvement. While performance is indeed a valid concern — it can be mitigated (see “Component testing” bullet), however, test complexity is a much painful sorrow that should govern other considerations most of the time. Practically, make each test case explicitly add the DB records it needs and act only on those records. If performance becomes a critical concern — a balanced compromise might come in the form of seeding the only suite of tests that are not mutating data (e.g. queries)
<br/>

❌ **Otherwise:** Few tests fail, a deployment is aborted, our team is going to spend precious time now, do we have a bug? let’s investigate, oh no — it seems that two tests were mutating the same seed data

<br/>

<details><summary>✏ <b>Code Examples</b></summary>

<br/>

### :thumbsdown: Anti Pattern Example: tests are not independent and rely on some global hook to feed global DB data

![](https://img.shields.io/badge/🔧%20Example%20using%20Mocha-blue.svg "Examples with Mocha")

```javascript
before(() => {
  //adding sites and admins data to our DB. Where is the data? outside. At some external json or migration framework
  await DB.AddSeedDataFromJson('seed.json');
});
it("When updating site name, get successful confirmation", async () => {
  //I know that site name "portal" exists - I saw it in the seed files
  const siteToUpdate = await SiteService.getSiteByName("Portal");
  const updateNameResult = await SiteService.changeName(siteToUpdate, "newName");
  expect(updateNameResult).to.be(true);
});
it("When querying by site name, get the right site", async () => {
  //I know that site name "portal" exists - I saw it in the seed files
  const siteToCheck = await SiteService.getSiteByName("Portal");
  expect(siteToCheck.name).to.be.equal("Portal"); //Failure! The previous test change the name :[
});

```

<br/>

### :clap: Doing It Right Example: We can stay within the test, each test acts on its own set of data

```javascript
it("When updating site name, get successful confirmation", async () => {
  //test is adding a fresh new records and acting on the records only
  const siteUnderTest = await SiteService.addSite({
    name: "siteForUpdateTest"
  });

  const updateNameResult = await SiteService.changeName(siteUnderTest, "newName");

  expect(updateNameResult).to.be(true);
});
```

</details>

<br/>


## ⚪ ️ 1.10 Don’t catch errors, expect them

:white_check_mark: **Do:** When trying to assert that some input triggers an error, it might look right to use try-catch-finally and asserts that the catch clause was entered. The result is an awkward and verbose test case (example below) that hides the simple test intent and the result expectations

A more elegant alternative is the using the one-line dedicated Chai assertion: expect(method).to.throw (or in Jest: expect(method).toThrow()). It’s absolutely mandatory to also ensure the exception contains a property that tells the error type, otherwise given just a generic error the application won’t be able to do much rather than show a disappointing message to the user
<br/>

❌ **Otherwise:** It will be challenging to infer from the test reports (e.g. CI reports) what went wrong

<br/>

<details><summary>✏ <b>Code Examples</b></summary>

<br/>

### :thumbsdown: Anti-pattern Example: A long test case that tries to assert the existence of error with try-catch

![](https://img.shields.io/badge/🔧%20Example%20using%20Mocha-blue.svg "Examples with Mocha")

```javascript
it("When no product name, it throws error 400", async () => {
  let errorWeExceptFor = null;
  try {
    const result = await addNewProduct({});
  } catch (error) {
    expect(error.code).to.equal("InvalidInput");
    errorWeExceptFor = error;
  }
  expect(errorWeExceptFor).not.to.be.null;
  //if this assertion fails, the tests results/reports will only show
  //that some value is null, there won't be a word about a missing Exception
});
```

<br/>

### :clap: Doing It Right Example: A human-readable expectation that could be understood easily, maybe even by QA or technical PM

```javascript
it("When no product name, it throws error 400", async () => {
  await expect(addNewProduct({}))
    .to.eventually.throw(AppError)
    .with.property("code", "InvalidInput");
});
```

</details>

<br/><br/>

## ⚪ ️ 1.11 Tag your tests

:white_check_mark: **Do:** Different tests must run on different scenarios: quick smoke, IO-less, tests should run when a developer saves or commits a file, full end-to-end tests usually run when a new pull request is submitted, etc. This can be achieved by tagging tests with keywords like #cold #api #sanity so you can grep with your testing harness and invoke the desired subset. For example, this is how you would invoke only the sanity test group with Mocha: mocha — grep ‘sanity’
<br/>

❌ **Otherwise:** Running all the tests, including tests that perform dozens of DB queries, any time a developer makes a small change can be extremely slow and keeps developers away from running tests

<br/>

<details><summary>✏ <b>Code Examples</b></summary>

<br/>

### :clap: Doing It Right Example: Tagging tests as ‘#cold-test’ allows the test runner to execute only fast tests (Cold===quick tests that are doing no IO and can be executed frequently even as the developer is typing)

![](https://img.shields.io/badge/🔧%20Example%20using%20Jest-blue.svg "Examples with Jest")

```javascript
//this test is fast (no DB) and we're tagging it correspondigly
//now the user/CI can run it frequently
describe("Order service", function() {
  describe("Add new order #cold-test #sanity", function() {
    test("Scenario - no currency was supplied. Expectation - Use the default currency #sanity", function() {
      //code logic here
    });
  });
});
```

</details>

<br/><br/>

## ⚪ ️ 1.12 Categorize tests under at least 2 levels

:white_check_mark: **Do:** Apply some structure to your test suite so an occasional visitor could easily understand the requirements (tests are the best documentation) and the various scenarios that are being tested. A common method for this is by placing at least 2 'describe' blocks above your tests: the 1st is for the name of the unit under test and the 2nd for additional level of categorization like the scenario or custom categories (see code examples and prtscn below). Doing so will also greatly improve the test reports: The reader will easily infer the tests categories, delve into the desired section and correlate failing tests. In addition, it will get much easier for a developer to navigate through the code of a suite with many tests. There are multiple alternative structures for test suite that you may consider like [given-when-then](https://github.com/searls/jasmine-given) and [RITE](https://github.com/ericelliott/riteway)

<br/>

❌ **Otherwise:** When looking at a report with flat and long list of tests, the reader have to skim-read through long texts to conclude the major scenarios and correlate the commonality of failing tests. Consider the following case: When 7/100 tests fail, looking at a flat list will demand reading the failing tests text to see how they relate to each other. However, in an hierarchical report all of them could be under the same flow or category and the reader will quickly infer what or at least where is the root failure cause

<br/>

<details><summary>✏ <b>Code Examples</b></summary>

<br/>

### :clap: Doing It Right Example: Structuring suite with the name of unit under test and scenarios will lead to the convenient report that is shown below

![](https://img.shields.io/badge/🔧%20Example%20using%20Jest-blue.svg "Examples with Jest")

```javascript
// Unit under test
describe("Transfer service", () => {
  //Scenario
  describe("When no credit", () => {
    //Expectation
    test("Then the response status should decline", () => {});

    //Expectation
    test("Then it should send email to admin", () => {});
  });
});
```

![alt text](assets/hierarchical-report.png)

<br/>

### :thumbsdown: Anti-pattern Example: A flat list of tests will make it harder for the reader to identify the user stories and correlate failing tests

![](https://img.shields.io/badge/🔧%20Example%20using%20Jest-blue.svg "Examples with Mocha")

```javascript
test("Then the response status should decline", () => {});

test("Then it should send email", () => {});

test("Then there should not be a new transfer record", () => {});
```

![alt text](assets/flat-report.png)

<br/>

</details>

<br/><br/>

## ⚪ ️1.13 Other generic good testing hygiene

:white_check_mark: **Do:** This post is focused on testing advice that is related to, or at least can be exemplified with Node JS. This bullet, however, groups few non-Node related tips that are well-known

Learn and practice [TDD principles](https://www.sm-cloud.com/book-review-test-driven-development-by-example-a-tldr/) — they are extremely valuable for many but don’t get intimidated if they don’t fit your style, you’re not the only one. Consider writing the tests before the code in a [red-green-refactor style](https://blog.cleancoder.com/uncle-bob/2014/12/17/TheCyclesOfTDD.html), ensure each test checks exactly one thing, when you find a bug — before fixing write a test that will detect this bug in the future, let each test fail at least once before turning green, start a module by writing a quick and simplistic code that satsifies the test - then refactor gradually and take it to a production grade level, avoid any dependency on the environment (paths, OS, etc)
<br/>

❌ **Otherwise:** You‘ll miss pearls of wisdom that were collected for decades

<br/><br/>

#### [`Section 2: Backend`](/section-2️-backend-testing)
