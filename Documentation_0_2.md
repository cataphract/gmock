# Introduction #

Gmock is a mocking framework for the Groovy language.

Gmock is all about simple syntax and readability of your tests so you spend less time learning the framework and more time writing code. To use Gmock just drop the gmock jar file into your classpath and make sure you also have junit.


This documention describes the version 0.2 of Gmock.


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

If you don't care how many times a method is called or not called at all you can stubbed it.
```
def loader = mock()
loader.put("fruit").returns("apple").stub()
play {
  assertEquals "apple", loader.put("fruit")
  assertEquals "apple", loader.put("fruit")
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