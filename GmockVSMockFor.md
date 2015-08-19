# Basic Usages #

### MockFor ###
```
// A Stub's expectation is sequence independent
def stub = new StubFor(HashMap) // you have to mock a class instead of an interface
stub.demand.get { key ->
  assert 1 == key
  'one'
}
stub.demand.clear { ->
  throw new RuntimeException()
}
stub.use {
  def mockMap = new HashMap() // this object has to be created in the use closure
  someCodeThatInteractsWithMock(mockMap)
}
stub.expect.verify() // use of verify() is left to the user
```

### Gmock ###
```
def mockMap = mock(Map)
mockMap.get(1).returns('one')
mockMap.clear().raises(RuntimeException)
play {
  someCodeThatInteractsWithMock(mockMap)
}
```


# Order Checking #

### MockFor ###
```
// A Mock's expectation is always sequence dependent
def mock = new MockFor(HashMap)
mock.demand.put { key, value ->
  assert 'one' == key
  assert 1 == value
}
mock.demand.put { key, value ->
  assert 'two' == key
  assert 2 == value
}
mock.use {
  def mockMap = new HashMap()
  someCodeThatInteractsWithMock(mockMap)
}
```

### Gmock ###
```
def mockMap = mock(Map)
ordered {
  mockMap.put('one', 1)
  mockMap.put('two', 2)
}
play {
  someCodeThatInteractsWithMock(mockMap)
}
```
Gmock also support order checking of multiple mocks, while MockFor doesn't.
```
def one = mock(Map)
def two = mock(Map)
ordered {
  one.put('one', 1)
  two.put('two', 2)
}
play {
  someCodeThatInteractsWithMocks(one, two)
}
```


# Times Verification #

### MockFor ###
```
mock = new MockFor(HashMap)
mock.demand.clear(3..3) {}
mock.demand.put(1..<Integer.MAX_VALUE) { key, value ->
  assert 1 == key
  assert 2 == value
}
mock.demand.get(0..<Integer.MAX_VALUE) { key ->
  assert 1 == key
  return 2
}
mock.use {
  def mockMap = new HashMap()
  someCodeThatInteractsWithMock(mockMap)
}
```

### Gmock ###
```
def mockMap = mock(Map) {
  clear().times(3)
  put(1, 2).atLeastOnce()
  get(1).returns(2).stub()
}
play {
  someCodeThatInteractsWithMock(mockMap)
}
```


# Property Mocking #

### MockFor ###
```
def mock = new MockFor(HashMap)
mock.demand.setSomething { value -> assert 'value' == value }
mock.demand.getSomething { -> 'something' }
mock.use {
  def mockMap = new HashMap()
  someCodeThatInteractsWithMock(mockMap)
}
```

### Gmock ###
```
def mockMap = mock(Map)
mockMap.something.set('value')
mockMap.something.returns('something')
play {
  someCodeThatInteractsWithMock(mockMap)
}
```


# Static Method Mocking #

### MockFor ###
There are no differences between static method mocking and instance method mocking in MockFor.
```
def mock = new MockFor(HashMap)
mock.demand.get(1..2) { -> 'static' }
mock.use {
  assert 'static' == HashMap.get()
  assert 'static' == new HashMap().get()
}
```

### Gmock ###
```
def mockMap = mock(HashMap)
mockMap.static.get().returns('static')
play {
  assert 'static' == HashMap.get()
  // this will not pass:
  // assert 'static' == new HashMap().get()
}
```
Or even shorter:
```
mock(HashMap).static.get().returns('static')
play {
  assert 'static' == HashMap.get()
}
```


# Constructor Mocking #

### MockFor ###
There is no constructor mocking in MockFor. You have to use ExpandoMetaClass instead.
```
HashMap.metaClass.constructor = { String s ->
  assert 'one' == s
  new HashMap()
}
mock = new MockFor(HashMap)
mock.demand.get { key -> 'two' }
mock.demand.put { key, value -> assert 'three' == value }
mock.use {
  def mockMap = new HashMap('one')
  assert 'two' == mockMap.get(2)
  mockMap.put(3, 'three')
}
```

### Gmock ###
```
mock(HashMap, constructor('one')) {
  get(anything()).returns('two')
  put(anything(), 'three')
}
play {
  def mockMap = new HashMap('one')
  assert 'two' == mockMap.get(2)
  mockMap.put(3, 'three')
}
```