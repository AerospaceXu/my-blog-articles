# Go 实现单例模式

## 1 单例模式的简单介绍

单例模式的实现大体上有两种：懒汉式和饿汉式。

### 1.1 懒汉

从懒汉这两个字，我们就能知道，这个模式很懒。

所以它**不可能在未使用实例时就创建了对象**，懒汉只有在使用时才会创建单例。

### 1.2 饿汉

饿汉因为饿呀，所以很着急的就创建了实例，不用等到使用时才创建。

这样我们每次调用获取接口将不会重新创建新的对象，而是直接返回之前创建的对象。比较适用于：如果某个单例使用的次数少，并且创建单例消息的资源比较多，那么就需要实现单例的按需创建，这个时候懒汉模式就是一个不错的选择。不过也有缺点，饿汉模式将在包加载的时候就会创建单例对象，当程序中用不到该对象时，浪费了一部分空间，但是相对于懒汉模式，不需要进行了加锁操作，会更安全，但是会减慢启动速度。

## 2 Go 语言实现

### 2.1 懒汉式

**不加锁**

```go
package singleton

type singleton struct {
	A int
}

var instance *singleton

// GetInstance 返回唯一的实例对象
func GetInstance() *singleton {
	if instance == nil {
		instance = &singleton{}
	}
	return instance
}
```

这是比较好想的一种方式，但是如果处于高并发的情况下，两处位置同时调用了 `GetInstance` 方法，在 if 的时候同时判断为 nil，最终就会出现错误。
