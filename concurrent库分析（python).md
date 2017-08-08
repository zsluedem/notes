# concurrent库分析（python)
##future object

future状态：

* PENDING
* RUNNING
* CANCELLED
* CANCELLED_AND_NOTIFIED
* FINISHED

future的几个私有属性：

```python
self._condition = threading.Condition()
self._state = PENDING
self._result = None
self._exception = None
self._waiters = []
self._done_callbacks = []
```

## 错误类型
父类：Error

* CancelledError
* TimeoutError

##
