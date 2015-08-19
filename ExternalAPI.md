# External API #

This is a work in progress describing the external gmock API.


# The work in progess #

```

// mocks creation
def mock = mock() 
def mock = mock(SomeClass) // strong typing support

// constructor mocking
def mock = mock(SomeClass, constructor: ["arg1", "arg2"])

// method mocking
mock.load(1, 2).returns(3) 
mock.load(2, 3).raises(SomeException)
mock.load(3, 4).raises(SomeException, "error message")
mock.load(4, 5).raises(new SomeException())

// argument matchers
mock.put(match{ it > 1 }, match{ it in 3..5 }) // closure matchers
mock.put(greaterThan(1), allOf(greaterThanOrEqualTo(3), lessThanOrEqualTo(5))) // hamcrest matchers

// time matching
mock.put(1, 2).times(1..2)  
mock.put(1, 2).times(3)
mock.put(1, 2).anyTimes()
mock.put(1, 2).never()
mock.put(1, 2).atLeastOnce()


// property mocking
mock.name.sets("some name")
mock.name.returns("other name")

// static mocking
mock.static.load(1,2).returns(3)


strict { // strict ordering

}
play {
       // the code under tests
}

```

# Some proposals from Johnny #

Simplest mock:
```
def mock = mock()
// only these methods are expected, invoking can be in any order
mock.load(1, 2).returns(3)
mock.load(2, 3).raises(SomeException)
mock.put(1, 2)
play {
  // test
}
```

Other kinds of mocking can use dynamic methods:
```
def mock1 = stubMock() // the methods can be invoked for any times
def mock2 = strictMock() // method calls in strict order
def mock3 = niceMock() // unexpected methods return empty values
def mock4 = stubAndNiceMock() // combine stub mock and nice mock
// also strictAndNiceMock(), niceAndStubMock() and niceAndStrictMock(), but no stubAndStrictMock()
```

The kinds of these mocks(except nice mock) are only available without specifing the kind of method. The kind of method can be specified by "type methods" and "type closures":
```
def mock = stubMock()
mock.load(1, 2).returns(3) // stub
mock.put(1, 2).expected() // type changed
mock.put(2, 3).stubbed() // definitely specify to be stub type, but not necessary
mock.get().returns(4).strict() // strict type can be specified by type method
expect { // type closures
  // ...
}
stub {
  // ...
}
strict {
  // ...
}
play {
  // test
}
```

# Closure mocking proposals #

First:
```
mockServer.multipleRunner(capture(), capture())
mockServer.run('ls')
mockServer.run('whoami')
// nested closure
mockServer.multipleRunner(capture(), capture())
mockServer.run('ls', capture())
mockServer.parameter('/home')
mockServer.run('whoami')
play {
    doSomething()
}
```

Second:
```
mockServer.multipleRunner(
    invoke{ run('ls') },
    invoke{ run('whoami') }
)
// nested closure
mockServer.multipleRunner(
    invoke{
        run('ls', invoke{ parameter('/home') })
    },
    invoke{
        run('whoami')
    }
)
play {
    doSomething()
}
```