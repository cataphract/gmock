# Basic Usages #

### EasyMock ###
```
List mock = createMock(List.class);

expect(mock.get(0)).andReturn("one");
expect(mock.get(1)).andStubReturn("two");
mock.clear();
expectLastCall().andThrow(new RuntimeException());

replay(mock);

someCodeThatInteractsWithMock(mock);

verify(mock);
```

### Gmock ###
```
def mock = mock(List)

mock.get(0).returns('one')
mock.get(1).returns('two').stub()
mock.clear().raises(RuntimeException)

play {
  someCodeThatInteractsWithMock(mock)
}
```
Or even shorter:
```
def mock = mock(List) {
  get(0).returns('one')
  get(1).returns('two').stub()
  clear().raises(RuntimeException)
}

play {
  someCodeThatInteractsWithMock(mock)
}
```


# Order Checking #

### EasyMock ###
```
Control control = createStrictControl();

List one = control.createMock(List.class);
List two = control.createMock(List.class);

expect(one.add("one")).andReturn(true);
expect(two.add("two")).andReturn(true);

control.replay();

someCodeThatInteractsWithMocks(one, two);

control.verify();
```

### Gmock ###
```
def one = mock(List)
def two = mock(List)

ordered {
  one.add("one").returns(true)
  two.add("two").returns(true)
}

play {
  someCodeThatInteractsWithMocks(one, two)
}
```


# Times Verification and Argument Matchers #

### EasyMock ###
```
List mock = createMock(List.class);

mock.clear();
expectLastCall().times(3);

expect(mock.add(anyObject())).andReturn(true).atLeastOnce();

replay(mock);

someCodeThatInteractsWithMock(mock);

verify(mock);
```

### Gmock ###
```
def mock = mock(List)

mock.clear().times(3)
mock.add(anything()).returns(true).atLeastOnce()

play {
  someCodeThatInteractsWithMock(mock)
}
```


# Custom Argument Matchers #

### EasyMock ###
It is inconvenient to create a custom argument matcher in EasyMock.

First, you have to implement IArgumentMatcher:
```
import org.easymock.IArgumentMatcher;
public class ThrowableEquals implements IArgumentMatcher {
  private Throwable expected;
  public ThrowableEquals(Throwable expected) {
    this.expected = expected;
  }
  public boolean matches(Object actual) {
    String actualMessage = ((Throwable) actual).getMessage();
    return expected.getClass().equals(actual.getClass())
        && expected.getMessage().equals(actualMessage);
  }
  public void appendTo(StringBuffer buffer) { ... }
}
```

Then, define a static method to report the matcher:
```
public static <T extends Throwable> T eqException(T in) {
  reportMatcher(new ThrowableEquals(in));
  return null;
}
```

Finally, you can use the matcher:
```
IllegalStateException e = new IllegalStateException("Operation not allowed.")
expect(mock.logThrowable(eqException(e))).andReturn(true);
```

### Gmock ###
In Gmock, all you need is just defining a closure:
```
mock.logThrowable(match { it?.class == IllegalStateException && it.message == "Operation not allowed." }).returns(true)
```