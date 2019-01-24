# Flask 를 간단 하게 시작 하기  

## Flask 실행 하기  

## 동적으로 module import 및 함수 Name으로 실행 하는 법
```python
testmod = __import__('testmodule')
result = getattr(testmod, 'test_func')()
print(result)
```
