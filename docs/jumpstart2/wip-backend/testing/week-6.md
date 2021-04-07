# Week 5: Advanced Backend and Testing

## Agenda
- Backend Basics Cont.
- Testing
  - JUnit 5
  - Mockito
- Advanced Backend
  - DTOs in General
  - Unmarshalling objects into DTOs
  - Backend Validation
  - Other Request Types
  
## Basic Plan
1. Talk about testing in the software development world
  - Types of tests
  - Basic testing with JUnit
    - Test Class Locations
    - Show basic test examples
    - `BeforeEach`/`BeforeAll` annotations
    - Parameterized tests
    - Exceptions
  - Mocking objects with Mockito
    - Mocking behavior and returns
    - Verification
    - Argument Captors
    - `Externals` Class
    - Show the test created for the backend api module
    
2. New Request Types
  - `POST`, `PUT`, `PATCH`, and `DELETE`: what are they commonly used for
  
3. DTOs and Marshalling/Unmarshalling
  - What are DTOs
    - What are they used for
  - Marshalling/Unmarshalling
  - Validating unmarshalled objects/why it's necessary
 
## Testing

### Types of Tests

You may be able to tell from the name that testing is a method used to help prove that your code works the way you expect 
it to. If you haven't spent a lot of time working on tests before, whether that's because you're new to development or 
never thought 
that tests were important enough to spend time on, just know that they're extremely important in helping you 
diagnose issues in your code (if you write meaningful tests) before releases or after making significant changes. 
By the way, if you've heard of check-expects from fundies, they're basically the same thing! For smaller projects, tests 
have less of an impact, but they're still helpful. As your project grows and begins incorporating work from more 
people, however, they'll save you a lot of time in tracking down and preventing a lot of bugs that come from
unexpected interactions or mistakes that were made in trying to solve a problem.

In software development, there are around seven types of tests that you'll probably interact with throughout 
your career, and different organizations may combine or rename a lot of these. We're calling them 'unit', 'integration', 
'functional', 'end-to-end', 'acceptance', 'performance', and 'smoke' tests.

- *Unit* tests are meant to test functions/methods. They are basic tests and work with the smallest units of 
functionality in your code.
- *Integration* tests ensure that multiple different units, modules (groups of code), or applications can successfully 
work together and produce the response you're expecting.
- *End-To-End* tests try to act as a user and go through different workflows to ensure large
parts of the application are working as expected.
- *Acceptance* tests verify business requirements (and sometime performance) in a formal way to determine if business 
goals were met.
- *Performance* tests measure the performance of the system under heavy load. They determine speed, reliability,
stability, availability, and other attributes of the application when thoroughly stressed.
- *Smoke* tests check basic functionality quickly to prove that major systems are working as expected.

For more info, check out [Atlassian's document on different types of software 
testing](https://www.atlassian.com/continuous-delivery/software-testing/types-of-software-testing).

### Testing in Java with JUnit 5

JUnit is a library which provides a lot of help with testing things in Java. It allows you to do a lot of really useful
stuff when testing, like:

- Declaring a test using the `@Test` annotation
- Run multiple tests with different inputs using the `@ParameterizedTest` annotation
- Run setup/teardowns for each test or all tests using the `@BeforeAll`/`@BeforeEach` and `@AfterAll`/`@AfterEach` 
annotations
- Asserting a ton of different things about tests using methods like `assertTrue`, `assertFalse`, `assertNull`, 
`assertNotNull`, `assertEquals`, and many more
- Assumptions (tests that only run if a preliminary tests succeeds)
- Tests that are repeated a number of times using `@RepeatedTest`

Check out the [JUnit 5 Documentation page](https://junit.org/junit5/docs/current/user-guide/).

#### Test Class Locations

Tests are usually placed in a directory in <module>/src/test/, which mirrors the <module>/src/main/ directory.
Test classes are placed in the same package (within the test directory) of the class they are testing.
For example, the `PostsProcessor` class in service/src/**main**/java/com.codeforcommunity/processor/ will be tested by a 
class named something like `PostsProcessorTest` in the service/src/**test**/java/com.codeforcommunity/processor/ directory.

#### Basic Tests

Most of the time, a test is started with the `@Test` annotation. Then it will perform some actions, do a couple
of assertions, and end. If it fails, either Maven or the IntelliJ test window will let you know which tests failed and 
why.

Here is an example:
```java
// Returns true if i is > 0
private boolean isPositive(int i) {
    return i > 0;
}

// Generate numbers 1 to 10 in a list
private List<Integer> generateNumbers() {
    List<Integer> nums = new ArrayList();
    for (int i = 0; i < 10; i++) {
        nums.add(i + 1);
    }
}

// Makes sure generateNumbers created a list of numbers from 1 to 10
@Test
public void testUsingAssertEquals() {
    List<Integer> nums = generateNumbers();

    assertNotNull(nums); // Make sure the list is not null
    assertEquals(10, nums.size()); // Make sure the list size is what we're expecting
    // You usually put what you're expecting before what you're testing (order technicaly doesn't matter, though)

    // Loop through everything in the list.
    for (int i = 0; i < nums.size(); i++) {
        assertTrue(isPositive(nums.get(i)); // Make sure each number is positive in the list
        assertEquals(i + 1, nums.get(i)); // Makes sure each item in the list is what is expected
    }
    assertNull(nums); // This one will cause the test to fail, since we know the list shouldn't be null
}
```

To run these tests once they've been added, you can just run `mvn` or `mvn install` if you want to do it through Maven.
The tests will be found and run automatically for you, since Maven is set up to look for IntelliJ tests.
If you'd prefer to do it in IntelliJ, all you have to do is hit the green play button, and choose Run or Debug.

>Note: for Maven to figure out that the method is a test, the method name needs to have the word 'test' somewhere in it.

#### BeforeEach, BeforeAll, AfterEach, and AfterAll

JUnit provides `@BeforeEach`, `@BeforeAll`, `@AfterEach`, and `@AfterAll` annotations. These allow you to perform 
methods before/after every/each test if it helps to set up or clean up tests without you having to call them manually.

>Note: for the "All" methods, the methods have to be static to work.

Here is an example:
```java
@BeforeEach
public void setup() {
    this.db = new DatabaseImplementation();
    this.processor = new ProcessorThing(this.db);
}
```

The above code will then run before each test in the test suite and set up the processor and database for you.

#### Parameterized Tests

You can also run multiple tests using different input parameters by using the `@ParameterizedTest` 
annotation. 

You can add specific inputs to the `@ParameterizedTest` by adding one or more 'source' annotations.
Three of the most basic ones are `@NullSource`, `@EmptySource` (provides an empty String, List, Set, Map, or any 
array type), and the combination `@NullAndEmptySource`. Those can be combined with `@ValueSource` (or you can use them 
alone if that suits your needs better) to test with a simple set of values.

The `@ValueSource` annotation allows you to provide a list of values of any atomic type (short, byte, int, long, float,
double, char, boolean), as well as String and `Class` types. 

>Note: the input to the annotation must be the pluralized name of the type you're inputting. As an example, a list of 
>integer inputs would be `@ValueSource(ints = {1, 2, 3, ...})`, and booleans would 
>be `@ValueSource(booleans = {true, false})`.

The `@EnumSource` conveniently provides all sub-types of a provided enum class.

If none of those suit your needs, you can also use `@MethodSource`, `@CsvSource`, `@CsvFileSource`, and 
`@ArgumentSource`.

You can see all of the sources of parameterized arguments [on the JUnit 
docs](https://junit.org/junit5/docs/current/user-guide/#writing-tests-parameterized-tests-sources).

Here is an example of a parameterized test using integer inputs:

```java
@ParameterizedTest
@ValueSource(ints = {1, 2, 3})
@NullSource
// Notice how we're using the wrapped integer class "Integer" since we're inputting a null source.
public void testParameterizedTest(Integer value) { 
    if (value == null) {
        assertNull(value);
    }
    else {
        assertTrue(value > 0 && value < 4);
    }
}
```

#### Exceptions

An exception is an error produced by your code or dependencies telling you about something that went wrong.
When testing exceptions, JUnit provides methods and annotations to help, but we find it more useful to use 
try/catch blocks because they allow you to test the contents of an exception more easily.

Example:
```java 
@Test
public void someTestMethod() {
    try {
        thingThatThrowsException();
        fail(); // fail this test if we make it this far
    }
    catch (TheExpectedExceptionType e) {
        assertEquals("My exception message", e.getMessage());
        assertEquals(otherThingsAboutExceptionIfYouWant, e.doSomething());
    }
}
```

### Testing Using Mockito

Mockito is a really useful library for mocking the interactions between objects that you don't have control over
(like dependencies). It lets you create fake versions of objects which you have complete control over, and it even lets
you verify things about how it was called.

If you're interested in learning more about Mockito, [here is the 
documentation](http://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html). It even lets you do some 
really cool things like `spy()`, where you can pass in an existing object, have it do _most_ of the things it usually 
does, and then just override/verify/mock what you want!

#### Mocking an Object and Overriding Returns

To mock an object, all you have to do is call (the static method) `Mockito.mock(Thing.class)`, which will return a fake
instance of that `Thing` class/interface. By default, that fake instance will return `null`
for every non-void method you call, but you can override that using `thenReturn`, `thenThrow`, `then` (`thenAnswer`, 
allows you to do custom stuff), or `doReturn`, `doThrow`, `doAnswer`, `doNothing`, `doCallRealMethod` in the case of 
void returns. You can also chain these then/do methods together to have the instance return/throw/do each thing in order 
each time the overridden method is called.

You can read more about the tradeoffs between do... and then... 
[here](http://sangsoonam.github.io/2019/02/04/mockito-doreturn-vs-thenreturn.html#:~:text=In%20Mockito%2C%20a%20side%20effect%20happens%20to%20the%20real%20method%20invocation.&text=When%20you%20call%20a%20method,thenReturn%20has%20a%20type%20safety).
 
>An 'instance' of an object is a constructed value of the object created by its constructor. So for the class `Thing`, 
>it's the difference between referring to `Thing.staticMethod()` as a static method and `new Thing().instanceMethod()`.

Here are examples of the `then...` and `do...` styles of mocking:
```java 
MyCustomObject obj = mock(MyCustomObject.class); // can be done to interfaces too!

when(obj.myCustomMethod()).thenReturn("Hello World!");

String res = obj.myCustomMethod() // Returns "Hello World!");

assertEquals("Hello World!", res);
```

```java 
MyCustomObject obj = mock(MyCustomObject.class); // can be done to interfaces too!

doReturn("Hello World!").when(obj).myCustomMethod());

String res = obj.myCustomMethod() // Returns "Hello World!");

assertEquals("Hello World!", res);
```

If you only want to mock a method when it's called with certain parameters, then you can 
do that with argument matchers. Mockito defines the matcher `any()` for anything, `anyInt()` for any integer, 
`any(MyClass.class)` for a specific class, or even just enter your own number/string for specific arguments.
The result you specify will only be returned/done when all matchers are satisfied. 

>Note: if there's another condition you need satisfied, you can also create your own matcher if the default ones aren't 
>enough (I've never done that though, the default ones are usually good enough).

Example:
```java 
when(myMock.performMyOperation(anyInt()).thenReturn(5);

myMock.performMyOperation(5); // returns 5
myMock.performMyOperation(-1); // returns 5
myMock.performMyOperation(anyInt()); // you can't do this though, it only works in the "when" or "do..." methods
// This is because you're actually calling the method (or the mocked version), so you need to pass in a real value

when(myMock.otherMethod(any(OtherClass.class)).thenReturn(5);
myMock.otherMethod(new OtherClass()); // returns 5
myMock.otherMethod(new NotOtherClass()); // returns null, we didn't specify what to do here
```

#### Verification

You can also verify that methods on your mock were called.

Example:
```java 
Thing thing = mock(Thing.class);
thing.callSomeMethod(5);

// This will throw an exception if it wasn't called.
verify(thing).callSomeMethod(5);
verify(thing).callSomeMethod(any()); // this also takes argument matchers
verify(thing).callSomeMethod(6); // this will throw an exception
```

By default, the above `verify` will only pass if the method was called _exactly_ once. To verify that it wasn't called,
you can use `verify(thing, never())`. To verify that it was called exactly 5 times, you can use 
`verify(thing, times(5))`. To verify that it was called at least/most 2 times, you can use 
`verify(thing, atLeast(2))` or `verify(thing, atMost(2))`. There are other call count matchers available in the Mockito
documentation.

#### ArgumentCaptors

An `ArgumentCaptor` allows you to capture the argument that was used in a method on a mock. This is usually done
so that you can perform more exact tests on your mock. 

Example:
```java 
// Run your test prerequisites.

// Create your ArgumentCaptor.
ArgumentCaptor<SomeType> arg = ArgumentCaptor.forClass(SomeType.class);

verify(mock).doSomething(arg.capture());

// Then you can test things about your arg.
assertEquals("Hey!", arg.getMessage());
```

#### Externals Class

Sometimes you end up testing things where you want to override some behavior the method you're testing is using
and supply your own value, especially if it's something you can't control directly. For example, when getting a 
`Router` object in `PostsRouter`, we may want to control exactly what's returned from the `Router`'s methods. If we 
have our own version of a `Router` object that we want to supply for testing purposes, we need a way to substitute 
that in.

This is what our `PostsRouter` look like initially:
```java
public class PostsRouter implements IRouter {
    IPostsProcessor processor;

    // The basic constructor.
    public PostsRouter(IPostsProcessor processor) {
        this.processor = processor;
    }

    @Override
    public Router initializeRouter(Vertx vertx) {
        Router router = Router.router(vertx);
    ...
```

To reiterate the problem we have, how would we be able to test the `initializeRouter` method, but avoid *actually* 
calling `Router.router()`?

An `Externals` class (sometimes abbreviated to `externs`) can allow us to do that. We can define a 
`public static class Externals` as a nested class in `PostsRouter`, which has a method 
`public Router getRouter(Vertx vertx)`. This externs class will now allow us to override that `getRouter` behavior. 

>A nested class is a class that is created inside of another one. In general, you should avoid nested classes, but some
>reasons you may want to use them are:
>
>- It's a way of logically grouping related classes together
>- The nested class is allowed access to the outer class' private fields/methods
>- The nested class can be private (available to only the outer class) 
>
>Here's an example of one

```java 
public class OuterClass {
  public class NestedClass { }
}
```

>A **_static_** nested class is a nested class which doesn't depend on an instance of the outer class to be created.
>Usually, when creating a nested class, you need to create/have an outer class to create the nested class from, but
>static nested classes bypass that. However, if a nested class is declared static, then it can't access any non-static
>methods of its outer class.
>
>For example, if we have the `OuterClass` and `NestedClass` above, and we added the following `StaticNestedClass` to 
>the outer class, here is how we would access them.
>
>The StaticNestedClass.

```java 
... // in OuterClass
  public static class StaticNestedClass { }
```

>How to get instances of them.

```java 
// Non-static
OuterClass outerInstance = new OuterClass();
OuterClass.NestedClass nestedClass = outerInstance.new NestedClass();

// Static
OuterClass.StaticNestedClass staticNestedClass = new OuterClass.StaticNestedClass();
```

>Extending a class is when you create a 'subclass' which inherits all of the functions and fields of another 
>'superclass'. It's then able to define new functionality and 
>'override' existing functionality with the `@Override` annotation. In a really simple explanation, it allows you to
>take an existing class with all of its functionality, and selectively rewrite specific functionality you want to 
>override. This can be done for both non-final classes and abstract classes if you know what either of those are.
>See more information on [extending classes](https://www.tutorialspoint.com/java/java_inheritance.htm).

>Overriding a method (using the `@Override` annotation) allows you to define an implementation for a declared method
>of an interface or superclass which the given class is planning on implementing (for interfaces) or changing the 
>implementation of (for extended classes or interface default methods). The `@Override` lets your compiler perform some 
>checks for you to make sure that you're actually overriding methods that you're expecting to override.
>See more information on [overriding class or interface 
>methods](https://www.tutorialspoint.com/java/java_overriding.htm).

>Dependencies are external classes (in the case of Java and other Object-Oriented languages) or functions/methods that 
>are imported and which your code depends on. In compiled languages and sometimes when 
>[linting](https://stackoverflow.com/questions/8503559/what-is-linting), your code cannot be complied without access to 
>its dependencies (since it needs to know what functionality is provided). In the case of our 
>backend code, we have dependencies on Vert.x, Mockito, JUnit, and a few other libraries.


This is what our `PostsRouter` becomes with our externs:
```java
public class PostsRouter implements IRouter {
    IPostsProcessor processor;
    
    // Our new externs.
    Externals externs;

    // The basic constructor.
    public PostsRouter(IPostsProcessor processor) {
        this.processor = processor;
        this.externs = new Externals();
    }

    // The override constructor.
    PostsRouter(IPostsProcessor processor, Externals override) {
        this.processor = processor;
        this.externs = override;
    }

    public static class Externals {
        public Router getRouter(Vertx vertx) {
            return Router.router(vertx);
        }
    }

    @Override
    public Router initializeRouter(Vertx vertx) {
        Router router = externs.getRouter(vertx);
    ...
```

And this is how we override that behavior in the test class:
```java
public class PostsRouterTest {
    ...

    // Create the new externals, which overides the old behavior.
    public static class TestExterns extends Externals {
        public Router getRouter(Vertx vertx) {
            return myTestRouter;
        }
    }

    @BeforeEach
    public void setup() {
        ...
        this.processor = new Processor(this.db, new TestExterns());
        ...
    }
}
```

In the default `Externals` (the one in `PostsRouter`),  we'll start out just having that single `getRouter` method. 
Then, in our test class, we'll extend that `Externals` class with a new `TestExternals` class, and override the 
`getRouter` method to have it return our custom router object. This concept can be applied in many different ways
whenever a method has a dependency whose behavior we want to override when testing. 


#### A Full Mockito Example

Because this topic can be pretty complicated, we'll be providing an example below. Please note, we don't 
expect you to actually write these tests (you can if you want though). There's not much to test with Mockito on this 
project. Writing tests for the router is also a bit much, so Mockito is included in this project more as an
example of what is available to you if you end up doing more projects in Java. 

If you worked using the Care Package from the previous workshop, you probably noticed the `IPostTable` and 
`IPostsProcessor` interfaces. For this example, assume that the `IPostTable.deletePost(int postId)` and 
`IPostsProcessor.deletePost(int postId)` methods have been created. They do exactly what you'd think; check if a post
exists and delete it from the database.

For this example, we're going to pretend that the `IPostTable` interface and its implementations are an external
dependency (meaning we don't have control over the code), but we want to easily set it up for a test. If our
`IPostsProcessor.deletePost()` method looks like this:
```java 
public void deletePost(int postId) {
  // Make sure the post exists.
  if (!this.postTable.postExists(postId)) {
    throw new IllegalArgumentException("The post doesn't exist!");
  }

  ...

  // Then delete the post itself.
  postTable.deletePost(postId);
}
```

If, as we said, `IPostTable` is a dependency we don't have control over (so we can't just make a simple way to set it
up in the constructor, for example),  then we can mock it to make sure its methods are called and returned as we'd like.

Our test:
```java
@Test
public void mockitoValidateWithAnyInt() {
  // Look, we can even mock interfaces!
  IPostTable mockPostTable = Mockito.mock(IPostTable.class);
  // Set up what to do when postExists is called.
  Mockito.when(mockPostTable.postExists(Mockito.anyInt())).thenReturn(true);

  // Set up what to do when deletePost is called.
  // Note: since deletePost is a void method, we have to set it up using doThrow instead.
  String msg = "Oh no! You weren't supposed to call that!";
  // It's only going to get thrown when called with ID 5.
  Mockito.doThrow(new IllegalArgumentException(msg)).when(mockPostTable).deletePost(5);

  // We can now set up our processor with our mocked database.
  PostsProcessor newProcessor = new PostsProcessor(mockPostTable, this.commentTable);

  // Run the method.
  newProcessor.deletePost(100);

  // Make sure that postExists was called! We can use either 100 or anyInt() for this, depending
  // on whether or not we want to make sure that it was called with 100 or
  // just see if it was called.
  Mockito.verify(mockPostTable).postExists(100);
  // Make sure deletePost was called too! Let's switch it up and call it with anyInt() this time.
  Mockito.verify(mockPostTable).deletePost(Mockito.anyInt());

  // Now we're going to make it throw the exception.
  try {
    newProcessor.deletePost(5);
  } catch (IllegalArgumentException e) {
    // See if the messages are the same.
    assertEquals(msg, e.getMessage());
  }

  // Let's verify again. This time, since it's been called twice, we need to let Mockito know
  // we're expecting it to have been called more than once.
  Mockito.verify(mockPostTable, Mockito.times(2)).postExists(Mockito.anyInt());
  // But it should have only been called once with id = 5.
  Mockito.verify(mockPostTable).postExists(5);
  // And we can make sure deletePost was called the same way.
  Mockito.verify(mockPostTable, Mockito.times(2)).deletePost(Mockito.anyInt());
  Mockito.verify(mockPostTable).deletePost(5);
}
```

## New Request Types

Last week, we learned about the `GET` request type. There are a lot of other common request types too.

### Common Request Types

The most common request types (other than `GET`) are `POST`, `PUT`, `PATCH`, and `DELETE`. Of these, the one you 
probably see the most is `POST`. Every time you send a form by hitting a submit button, like when entering credit card 
information, typing in an address, or signing up for something, a `POST` request is sent with your data in the request 
body to the server. The other types aren't as common, but you've probably used them all a bunch without realizing it! 
When thinking in terms of CRUD (create, read, update, delete), this is what those types represent:

| Method | CRUD type | What it does |
| :---   | :---      | :-----       |
| GET    | Read      | Only _get_ information |
| POST   | Create    | Create new information |
| PUT    | Replace (Update) | Replace currently existing information |
| PATCH  | Modify (Update) | Modify currently existing information |
| DELETE | Delete | Delete information |

Requests can also have a request body (`GET` requests usually have all relevant information in its headers or path).
It's common that request bodies are in JSON format, but there are other formats that can be used too. `GET` requests 
are also the requests that are used in browsers when typing in a URL, while the other types may be used 
when you submit a form/log in or perform other operations. Because of this, `POST`, `PUT`, `PATCH`, and `DELETE` methods
are more closely related to each other than to `GET` requests.

See the [comparison between GET and POST requests](https://www.w3schools.com/tags/ref_httpmethods.asp).

On the backend side, to create a route for one of those other method types, you just substitute `router.get()` for
the request method you want to use. 

Examples:
```java 
Route route = router.post("/post/route");
```
```java 
Route route = router.put("/put/route");
```
```java 
Route route = router.patch("/patch/route");
```
```java 
Route route = router.delete("/delete/route");
```

### Other Request Types

There are many other request types that are used too. In practice, though, you'll probably never see them. The most 
commonly used requests are `GET` and `POST`, followed by `PUT`, `PATCH`, and `DELETE`.
MDN has a list of [other request types](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods) if you're interested.

## DTOs and Marshalling/Unmarshalling

### What are DTOs

DTOs, or Data Transfer Objects, are objects whose only purpose is to contain and transfer data around. They usually
only have methods for getting and setting data. Sometimes serialization and deserialization is included, but we'll 
mostly be focusing on the getting and setting part. 

>Note: serialization is the act of turning an object into a format, usually a string, that contains all of the 
>information needed to reconstruct the object. Deserialization is the opposite of that; it takes a serialized 
>representation, and turns it into the original object.

Here is an example of a DTO:
```java 
public class SampleDTO {
  private String name;
  private Integer id; // Note how we're using wrapper classes. DTOs don't have values set, and they may end up being 
    // null, so we can't use an 'int' type

  // For Unmarshalling (if your object will be unmarshalled ever). 
  private SampleDTO { }

  public SampleDTO(String name, Integer id) {
    this.name = name;
    this.id = id;
  }

  public void setName(String name) {
    this.name = name;
  }

  public String getName() {
    return this.name;
  }

  // Sometimes this is included for making sure the object is valid (see below, really only done on incoming requests).
  public void validate() {
    if (name == null) {
      throw ...
    }
    ...
  }
  ...
```

They're especially useful with REST requests and APIs because they can hold data from your incoming and outgoing 
requests.

### Marshalling/Unmarshalling

Marshalling and Unmarshalling is the process of transforming an object instance into a serialized representation  
of the object or vice versa. For our purposes, it's the process of turning a JSON into one of our DTOs.
There are a couple of libraries that are really useful for this, namely [Jackson](https://github.com/FasterXML/jackson)
and Vertx's [JsonObject](https://vertx.io/docs/apidocs/io/vertx/core/json/JsonObject.html) (it's actually Jackson!). 
Most marshallers/unmarshallers expect the names of the JSON and the class you're working with to 
match.

The following examples use the `SampleDTO` from above.

An example of Marshalling:
```java 
SampleDTO dto = new SampleDTO("me", 1);

String json = JsonObject.mapFrom(dto).encode(); // returns "{'name':'me','id':1}"
```

An example of Unmarshalling:
```java
String input = "{'name':'me','id':1'}";

SampleDTO dto = new JsonObject(input).mapTo(SampleDTO.class); // returns the unmarshalled object
```

>Note: In the `SampleDTO` class above, there is an empty, private constructor. This is required by your unmarshallers
>to instantiate a class before using 
>[introspection/reflection](https://www.oracle.com/technical-resources/articles/java/javareflection.html) to find all of 
>the required fields and load them into the class.

### Validating Unmarshalled Objects

After unmarshalling an object, it's important that you validate it to make sure that the fields have valid data. This
can be just making sure that integers have a positive values, no fields are null, strings aren't empty, or whatever
other basic data validation checks you need to perform. This needs to be done because users of your platform can't be
trusted to enter good data. It's always a good idea to make sure the data you are provided from a public facing API is 
what you're expecting, or you can find errors popping up in your projects. 

To make sure your DTOs have safe data inside, a `validate()` method is usually created in your DTOs (and it's
not a bad idea to put that into an interface which your DTOs will inherit). This method will be called after 
unmarshalling (or marshalling if you're worried about returning bad data to the front end) to check the DTO's fields and
validate them.

