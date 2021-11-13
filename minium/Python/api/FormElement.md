# `Class` minium.FormElement :id=minium-form-element
> `FormElement` 继承于  `BaseElement`, 提供了对表单类组件的操作 <br />
元素标签包含`input`, `textarea`, `switch`, `slider`, `picker`

!> 公共库 2.10.0 开始生效

通过调用[`Page.get_element`](minium/Python/api/Page?id=get_element)/[`Page.get_elements`](minium/Python/api/Page?id=get_elements)/[`Element.get_element`](minium/Python/api/Element?id=get_element)/[`Element.get_elements`](minium/Python/api/Element?id=get_elements)方法获取`Element`实例

---

## input() :id=input
> `input` & `textarea` 组件输入文字

> IDE上不会改变element上的value属性，建议使用变化的Page.data/hook绑定的input方法判断是否生效

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|text|str|None|输入文本|

**Returns:**
- `None`

**代码示例:** 

```python
import minium, threading, time
@minium.ddt_class
class TestElement(minium.MiniTest):
    def test_input(self):
        page = self.app.redirect_to("/pages/testelement/testelement")
        el = page.get_element("#username")
        callback_args = None
        callback_called = threading.Semaphore(0)
        def callback(args):
            nonlocal callback_args
            callback_args = args
            callback_called.release()
        self.app.hook_current_page_method("userNameInput", callback)
        el.input("test123")
        time.sleep(3)
        self.assertTrue(callback_called.acquire(timeout=10), "callback called")
        self.assertEqual(callback_args[0]["detail"]["value"], "test123", "input text ok")
```

---

## switch() :id=switch
> 改变 switch 组件的状态

**Parameters:**
- `None`

**Returns:**
- `None`

**代码示例:** 

```python
import minium, threading
@minium.ddt_class
class TestElement(minium.MiniTest):
    def test_switch(self):
        page = self.app.redirect_to("/pages/testelement/testelement")
        callback_args = None
        callback_called = threading.Semaphore(0)
        def callback(args):
            nonlocal callback_args
            callback_args = args
            callback_called.release()
        self.app.hook_current_page_method("switchChange", callback)
        el = page.get_element("switch")
        checked = el.checked
        el.switch()
        self.assertTrue(callback_called.acquire(timeout=10), "callback called")
        self.assertEqual(callback_args[0]["detail"]["value"], not checked, "switch ok")
        el.switch()
        self.assertTrue(callback_called.acquire(timeout=10), "callback called")
        self.assertEqual(callback_args[0]["detail"]["value"], not (not checked), "switch ok")
```

---

## slide_to() :id=slide_to
> slider 组件滑动到指定数值

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
| value|int|None|数值|

**Returns:**
- `None`

**代码示例:** 

```python
import minium, time
@minium.ddt_class
class TestElement(minium.MiniTest):
    def test_slide_to(self):
        page = self.app.redirect_to("/pages/testelement/testelement")
        element_slider = page.get_element("slider")
        element_slider.slide_to(80)
        time.sleep(1)
        self.assertEqual(element_slider.value, 80, "slider ok")
```

---

## pick() :id=pick
> picker 组件选值

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|value|看下表|Not None|属性名称|

**value 的取值：**

|选择器类型|类型| 说明|
| :----- | :-----: | :----- |
|selector: 普通选择器|int|表示选择了 range 中的第几个 (下标从 0 开始) |
|multiSelector: 多列选择器|int|表示选择了 range 中的第几个 (下标从 0 开始) |
|time: 时间选择器|str|表示选中的时间，格式为"hh:mm"|
|date: 日期选择器|str|表示选中的日期，格式为"YYYY-MM-DD"|
|region: 省市区选择器|int|表示选中的省市区，默认选中每一列的第一个值|

**Returns:**
- `None`

**代码示例:** 

```python
import minium, threading
@minium.ddt_class
class TestElement(minium.MiniTest):
    @classmethod
    def setUpClass(cls):
        super(TestElement, cls).setUpClass()
        cls.page = cls.app.redirect_to("/pages/testelement/testelement")

    @minium.ddt_unpack
    @minium.ddt_case(
        (0, 3, "bindPickerChange"),
        (1, "9:30", "bindTimeChange"),
        (2, "2019-10-31", "bindDateChange"),
    )  
    def test_pick(self, index, value, method):
        callback_args = None
        callback_called = threading.Semaphore(0)
        def callback(args):
            nonlocal callback_args
            callback_args = args
            callback_called.release()
        els = self.page.get_elements("picker")
        self.app.hook_current_page_method(method, callback)
        els[index].click()  # 用trigger模拟，阻止picker弹起
        els[index].pick(value)
        self.assertTrue(callback_called.acquire(timeout=10), "callback called")
        self.assertEqual(callback_args[0]["detail"]["value"], value, "pick ok")  

```

---