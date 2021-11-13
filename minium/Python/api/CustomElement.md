# `Class` minium.CustomElement :id=minium-custom-element
> `CustomElement` 继承于  `BaseElement`, 提供了对自定义组件的操作

通过调用[`Page.get_element`](minium/Python/api/Page?id=get_element)/[`Page.get_elements`](minium/Python/api/Page?id=get_elements)/[`Element.get_element`](minium/Python/api/Element?id=get_element)/[`Element.get_elements`](minium/Python/api/Element?id=get_elements)方法获取`Element`实例

**Properties:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|data|dict|Not None|自定义组件的 data, 支持 set、get|
|node_id|str|Not None|表示该组件为自定义组件|

---

## data :id=data
> 设置自定义组件的data

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|data|dict|Not None|data值|

**Returns:**
- `None`

**代码示例:** 

```js
// 被操作的小程序js代码
Component({
	data: {testdata: "hahahah"}
})
```

```python
#python代码
import minium
@minium.ddt_class
class TestElement(minium.MiniTest):
    def test_data(self):
        page = self.app.redirect_to("/pages/testelement/testelement")
        el = page.get_element("mytest")
        self.assertDictEqual({
            "testdata": "hahahah"
        }, el.data, "data equal")

```

---

## call_method() :id=call_method
> 调用自定义组件的方法

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|method|str|Not None|函数名称|
|args|array|None|函数参数|


**Returns:**
-  `dict`

**代码示例:**

```js
//被操作的小程序js代码
Component({
  methods: {
    callMethod: function(e) {
      e.message = "this is element test callMethod"
      return e
    }
  }
})
```

```python
# 测试case
import minium
@minium.ddt_class
class TestElement(minium.MiniTest):
    def test_call_method(self):
        page = self.app.redirect_to("/pages/testelement/testelement")
        el = page.get_element("mytest")
        result = el.call_method("callMethod", {"test": "test call method"})
        self.assertDictEqual(
            {"test": "test call method", "message": "this is element test callMethod"},
            result.get("result", {}).get("result"),
        )
```

---