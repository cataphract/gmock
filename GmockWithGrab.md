There is a class loader issue when using Gmock with @Grab. So the following script will fail with a `CodeGenerationException` caused by `ClassNotFoundException` of `org.gmock.internal.cglib.proxy.Factory`:

```
@Grab('org.gmock:gmock:0.8.0')
import org.gmock.GMockTestCase

class GMockWithGrabTest extends GMockTestCase {
    void testMock() {
        def m = mock() // throws a CodeGenerationException
        m.fun()
        play {
            m.fun()
        }
    }
}
```

We have to config grape to use system class loader as below:

```
@Grab('org.gmock:gmock:0.8.0')
@GrabConfig(systemClassLoader = true)
import org.gmock.GMockTestCase

class GMockWithGrabTest extends GMockTestCase {
    void testMock() {
        def m = mock() // mocks successfully
        m.fun()
        play {
            m.fun()
        }
    }
}
```