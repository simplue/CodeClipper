# python 装饰器实现请求超时重试
来源：[New Chat 21](http://127.0.0.1:5173/)

## 想法

## 摘录内容 
是的，我们可以将上面的代码改写成一个装饰器来实现请求超时重试的功能。下面是一个示例代码：

```
import requests

def retry_request(retry_times=3, timeout=5):
    def decorator(func):
        def wrapper(*args, **kwargs):
            for i in range(retry_times):
                try:
                    response = func(*args, **kwargs, timeout=timeout)
                    # 如果请求成功，返回响应对象
                    if response.status_code == 200:
                        return response
                except requests.exceptions.RequestException as e:
                    # 如果请求超时或者发生其他错误，打印错误信息并进行下一次重试
                    print(e)
            # 如果所有重试都失败了，返回 None
            return None
        return wrapper
    return decorator` 
```

在上面的代码中，我们定义了一个名为 `retry_request` 的装饰器函数，它接受两个参数：`retry_times` 和 `timeout`。`retry_times` 表示最大重试次数，`timeout` 表示请求超时时间。

装饰器函数返回一个内部函数 `decorator`，它接受一个函数作为参数，并返回一个新的函数 `wrapper`。在 `wrapper` 函数中，我们使用 `for` 循环来重试请求，最大重试次数为 `retry_times`。在循环中，我们调用传入的函数，并将 `timeout` 参数传递给它。如果请求成功，就返回响应对象；否则就打印出错误信息，并进行下一次重试。如果所有重试都失败了，就返回 `None`。

使用这个装饰器，我们可以在需要重试请求的函数上添加 `@retry_request()` 装饰器。例如：

pythonCopy code

```
@retry_request()
def get_response(url):
    return requests.get(url)` 
```

在上面的示例中，我们使用 `@retry_request()` 装饰器来修饰 `get_response` 函数，从而使它支持请求超时重试的功能。
