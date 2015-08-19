# Introduction #

Gmock is a mocking framework for the Groovy language.

Gmock is all about simple syntax and readability of your tests so you spend less time learning the framework and more time writing code. To use Gmock just drop the gmock jar file into your classpath and make sure you also have junit.


This documention describes the version 0.4 of Gmock.


# Getting Started #

```
import org.gmock.GMockTestCase

class LoaderTest extends GMockTestCase {
    void testLoader(){
        def mockLoader = mock()
        mockLoader.load('key').returns('value')
        play {
            assertEquals "value", mockLoader.load('key')
        }
    }
}  
```

  * First extend the `GMockTestCase`
  * Create mock objects using the `mock()` method
  * Set up expectations by calling the methods you expect on your mock
  * Run the code you are testing within the `play` closure


# Cookbook #

## Expectation and Play mode ##

Mock objects are created using the `mock()` method available in `GMockTestCase`. By default mock objects will record method calls and generate expectations.

The code under test should run through the `play` closure.
```
void testBasic(){
  def aMock = mock()
  // set up expectation
  play {
    // run your code 
  }
}
```

## Mock method call ##

Method call expectations are created when calling methods on Mock. Return values can be set up using the `returns` keyword.
```
def loader = mock()
loader.put("fruit").returns("apple")
play {
  assertEquals "apple", loader.put("fruit") 
}
```

Exceptions can be set up using the `raises` keyword.
```
def loader = mock()
loader.put("throw exception").raises(new RuntimeException("an exception"))
play {
  try {
    loader.put("throw exception") 
  } catch (RuntimeException e){
    assertEquals "an exception", e.message
  }
}
```


## Mock property call ##

Property calls should be mocked using the following syntax. For Setters and getters
```
def loader = mock()
loader.name.set("a name")
loader.name.returns("a different name")
play {
  loader.name = "a name"
  assertEquals "a different name", loader.name
}
```


Support for exceptions and method stubs are similar to standard method calls. Ex:
  * `loader.name.raises(new RuntimeException())`
  * `loader.name.set("invalid).raises(new RuntimeException())`
  * `mockLoader.name.returns('a name').stub()`


## Mock static method call ##

Mocking static method calls is similar to standard method calls, just add the static keyword:
```
def mockMath = mock(Math)
mockMath.static.random().returns(0.5)

play {
   assertEquals 0.5, Math.random()
}
```



## Mock constructor call ##

Constructor calls are mocked using the following syntax:
```
def mockFile = mock(File, constructor("/a/path/file.txt"))
```
This would match: `new File("/a/path/file.txt")`. The mockFile can then be used to set up further expectations.

Here is the full picture:
```
def mockFile = mock(File, constructor: ["/a/path/file.txt"])
mockFile.getName().returns("file.txt")
play {
  def file = new File("/a/path/file.txt")
  assertEquals "file.txt", file.getName()
}
```

## Time matching ##

Gmock lets you specify how many times an expectation can be called. Like here:
```
mockLoader.load(2).returns(3).atLeastOnce()
play {
    assertEquals 3, mockLoader.load(2)
    assertEquals 3, mockLoader.load(2)
}
```

The supported times matchers are:
  * **never()** the expectation should never be called
  * **once()** one time expectation (this is the implicit default)
  * **atLeastOnce()** one or more times
  * **atMostOnce()** zero or one time expectation
  * **stub()** the expectation can be called anytime
  * **times(3)** the expectation needs to be call n times
  * **times(2..4)** the expectation needs to be called n times within the range
  * **atLeast(4)** the expectation needs to be called at least n times
  * **atMost(4)** the expectation needs to be called at most n times

## Using matcher ##

You can set up a customised matcher in your expectation using the `match` syntax. The argument gets passed to match closure and will return true if it matches.

Here is the idea:
```
mockLoader.put("test", match { it > 5 }).returns("correct")
play {
  assertEquals "correct", mockLoader.put("test", 10)
}
```

Gmock is also fully compatible with the [Hamcrest](http://code.google.com/p/hamcrest/) matcher. You will have to include the optional Hamcrest library in your classpath

Here is an example:
```
mockLoader.put("test", is(not(lessThan(5)))).returns("correct")

play {
    assertEquals "correct", mockLoader.put("test", 10)
}
```

## Not extending the GMockTestCase ##

If you don't want to or can't extend the `GMockTestCase` in your test, you can use the GMockController.

At the beginning of your test, create a new `GMockController`. You then can use its `mock()` method and `play` closure as per a usual Gmock test.

```
void testController(){
  def gmc = new GMockController()
  def mockLoader = gmc.mock()
  mockLoader.load('key').returns('value')
  gmc.play {
    assertEquals "value", mockLoader.load('key')
  }
}
```