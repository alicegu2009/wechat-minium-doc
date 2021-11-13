# `Class` minium.BaseElement :id=minium-element
> `Element` 提供了对页面元素控件进行操作, 以及在控件内查找子控件的能力

通过调用[`Page.get_element`](minium/Python/api/Page?id=get_element)/[`Page.get_elements`](minium/Python/api/Page?id=get_elements)/[`Element.get_element`](minium/Python/api/Element?id=get_element)/[`Element.get_elements`](minium/Python/api/Element?id=get_elements)方法获取`Element`实例

**Properties:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|size|dict|Not None|元素宽高：{w, h}|
|offset|dict|Not None|元素偏移：{x, y}|
|rect|dict|Not None|元素位置：{x, y, w, h}|
|value|str|Not None|元素值|
|inner_text|str|Not None|元素文本|
|inner_wxml|str|Not None|获取元素子元素的 wxml（不包含本身）|
|outer_wxml|str|Not None|获取元素自身的 wxml（只包含其本身）|
|{attr}|str|Not None|Element对象的魔法糖，直接获取对应标签上的{attr}属性。如: `get_element("switch").checked`获取`switch`标签上的`checked`属性|

---

## get_element() :id=get_element
> 在当前控件内查询控件, 如果匹配到多个结果, 则返回第一个匹配到的结果

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|selector|str|Not None|选择器|
|inner_text|str|None|通过控件内的文字识别控件|
|text_contains|str|None|通过控件内的文字模糊匹配控件|
|value|str|None|通过控件的 value 识别控件|
|max_timeout|int|0|超时时间，单位 s|

*PS: selector 仅支持下列语法:*

- ID选择器：#the-id
- class选择器（可以连续指定多个）：.a-class.another-class
- 标签选择器：view
- 子元素选择器：.the-parent > .the-child
- 后代选择器：.the-ancestor .the-descendant
- 跨自定义组件的后代选择器：custom-element1>>>.custom-element2>>>.the-descendant <br>**custom-element1 和 .custom-element2必须是自定义组件标签或者能获取到自定义组件的选择器**
- 多选择器的并集：#a-node, .some-other-nodes
- **自定义组件不支持穿透，需要先get自定义组件, 再使用Element.get_element获取其子节点, 或使用[>>>]连接自定义组件及其后代元素**

**Returns:**
- [Element](minium/Python/api/element)

**代码示例:** 

```python
import minium
@minium.ddt_class
class TestElement(minium.MiniTest):
    @classmethod
    def setUpClass(cls):
        super(TestElement, cls).setUpClass()
        cls.page = cls.app.redirect_to("/pages/testelement/testelement")

	@minium.ddt_case(
        ("view", None, None, None, "first node", "first node", "<view>first node</view>"),
        (
            ".componentclass>>>test2>>>.componentclass",
            None,
            None,
            None,
            "test2 in test1\nthis is test2",
            '<view class="test--test2">test2 in test1</view><view><text>this is'
            " test2</text></view>",
            '<view class="customelement-test2--componentclass'
            ' customelement-test2--componentclass2"><view class="test--test2">test2 in'
            " test1</view><view><text>this is test2</text></view></view>",
        ),
    )
    def test_get_element(self, args):
        [
            selector,
            inner_text,
            text_contains,
            value,
            expected_inner_text,
            expected_inner_wxml,
            expected_outer_wxml,
        ] = args
        page = self.page.get_element("page")
        element = page.get_element(
            selector, inner_text=inner_text, text_contains=text_contains, value=value, max_timeout=5
        )
        self.assertEqual(expected_inner_text, element.inner_text, "inner text ok")
        self.assertEqual(expected_inner_wxml, element.inner_wxml, "inner wxml ok")
        self.assertEqual(expected_outer_wxml, element.outer_wxml)

```
更多例子见[Page.get_element](/minium/Python/api/Page#get_element), 两者用法一致

---

## get_elements() :id=get_elements
> 在当前控件内查询控件, 并返回一个或者多个结果

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|selector|str|Not None|选择器|
|max_timeout|int|0|超时时间，单位 s|
|inner_text|str|None|通过控件内的文字识别控件|
|text_contains|str|None|通过控件内的文字模糊匹配控件|
|value|str|None|通过控件的 value 识别控件|
|index|int|-1|index==-1: 获取所有符合的元素, index>=0: 获取前index+1符合的元素|

**PS: 支持的css选择器同 [get_element()](minium/Python/api/Element?id=get_element)**

**Returns:**
- List[[Element](minium/Python/api/element)]

**代码示例:** 

```python
import minium
@minium.ddt_class
class TestElement(minium.MiniTest):
    @classmethod
    def setUpClass(cls):
        super(TestElement, cls).setUpClass()
        cls.page = cls.app.redirect_to("/pages/testelement/testelement")

    @minium.ddt_case(
        (".testclass", 2),
        (".child", 3),
        (".parent .child", 2),
        (".componentclass>>>.componentclass", 3),
    )
    def test_get_elements(self, args):
        [selector, number] = args
        page = self.page.get_element("page")
        elements = page.get_elements(selector, max_timeout=5)
        self.assertEqual(number, len(elements))
```

---

## attribute() :id=attribute
> 获取元素属性

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|name|str \| list|Not None|属性名称|


**Returns:**
- 属性值列表, ` list`

**代码示例:** 

```python
import minium
@minium.ddt_class
class TestElement(minium.MiniTest):
    @classmethod
    def setUpClass(cls):
        super(TestElement, cls).setUpClass()
        cls.page = cls.app.redirect_to("/pages/testelement/testelement")

    @minium.ddt_case(
        ("style", "color: red; background-color: blue"),
        ("size", "mini"),
        (["style", "size"], ["color: red; background-color: blue", "mini"]),
    )
    def test_attribute(self, args):
        [attr, value] = args
        element = self.page.get_element(".testattr")
        values = element.attribute(attr)
        if len(values) == 1:
            self.assertEqual(value, values[0])
        else:
            self.assertListEqual(value, values)
```

---

## trigger() :id=trigger
> 触发元素事件

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|trigger_type|str|Not None|触发事件类型|
|detail|dict|None|触发事件时传递的 detail 值|

**Returns:**
- `None`

**代码示例:** 

```python
import minium, time
@minium.ddt_class
class TestElement(minium.MiniTest):
    def test_trigger(self):
        page = self.app.redirect_to("/pages/testelement/testelement")
        e = page.get_element("slider")
        e.trigger("change", {"value": 10})
        time.sleep(1)
        value = e.attribute("value")[0]
        self.assertEqual("10", value)
```

---


## tap() :id=tap
> 点击元素

**Parameters:**
- `None`

**Returns:**
- `None`
  
**代码示例:** 

```python
import minium
@minium.ddt_class
class TestElement(minium.MiniTest):
    def test_tap(self):
        page = self.app.redirect_to("/pages/testelement/testelement")
        e = page.get_element("#testtap1")
        e.tap()
        self.assertTrue(self.page.wait_for(".taptrigger", max_timeout=2))
        self.assertTrue(self.page.wait_for(".nottaptrigger", max_timeout=3))
```


---

## click() :id=click
> 点击元素, 同 [tap()](minium/Python/api/Element?id=tap)

**Parameters:**
- `None`

**Returns:**
- `None`
  
**代码示例:** 

```python
import minium
@minium.ddt_class
class TestElement(minium.MiniTest):
    def test_tap(self):
        page = self.app.redirect_to("/pages/testelement/testelement")
        e = page.get_element("#testtap1")
        e.click()
        self.assertTrue(self.page.wait_for(".taptrigger", max_timeout=2))
        self.assertTrue(self.page.wait_for(".nottaptrigger", max_timeout=3))
```
--- 

## long_press() :id=long_press
> 长按元素

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|duration|int|350|时长，ms|

**Returns:**
- `None`
  
**代码示例:**

```python
import minium
@minium.ddt_class
class TestElement(minium.MiniTest):
    def test_long_press(self):
        page = self.app.redirect_to("/pages/testelement/testelement")
        e = page.get_element("#testlongtap")
        e.long_press()
        self.assertTrue(page.wait_for(".taptrigger", max_timeout=2))
        self.assertTrue(page.wait_for(".nottaptrigger", max_timeout=3))
```
---

## move() :id=move
> 移动元素（触发元素的 touchstart、touchmove、touchend 事件）

!> 公共库 2.9.1 之后生效

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|x_offset|int|Not None|x 方向上的偏移，往右为正数，往左为负数|
|y_offset|int|Not None|y 方向上的偏移，往下为正数，往上为负数|
|move_delay|int|350|移动前摇，ms|
|smooth|bool|False|平滑移动|

**Returns:**
- `None`
  
**代码示例:**

```python
import minium, time
@minium.ddt_class
class TestElement(minium.MiniTest):
    @classmethod
    def setUpClass(cls):
        super(TestElement, cls).setUpClass()
        cls.page = cls.app.redirect_to("/pages/testelement/testelement")
        
    def _reset_movable_view(self):
        element = self.page.get_element("movable-view")
        element.move_to(0, 0)
        time.sleep(1)

    def test_move(self):
        self._reset_movable_view()
        element = self.page.get_element("movable-view")
        rect = element.rect
        element.move(30, 70, 500)
        self.assertDictEqual(
            {
                "left": rect["left"] + 30,
                "top": rect["top"] + 70,
                "width": rect["width"],
                "height": rect["height"],
            },
            element.rect,
        )

    def test_move_smooth(self):
        self._reset_movable_view()
        element = self.page.get_element("movable-view")
        rect = element.rect
        element.move(30, 70, 750, smooth=True)
        time.sleep(2)
        self.assertDictEqual(
            {
                "left": rect["left"] + 30,
                "top": rect["top"] + 70,
                "width": rect["width"],
                "height": rect["height"],
            },
            element.rect,
        )
```

---

## touch_start() :id=touch_start
> 触发元素的 touchstart 事件

!> 公共库 2.9.1 之后生效

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|touches|list|Not None|说明见[小程序文档](https://developers.weixin.qq.com/miniprogram/dev/framework/view/wxml/event.html#touches)|
|changed_touches|list|Not None|说明见[小程序文档](https://developers.weixin.qq.com/miniprogram/dev/framework/view/wxml/event.html#touches)|

**Returns:**
- `None`

**代码示例见[touch_move](#touch_move)**

---

## touch_move() :id=touch_move
> 触发元素的 touchmove事件

!> 公共库 2.9.1 之后生效

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|touches|list|Not None|说明见[小程序文档](https://developers.weixin.qq.com/miniprogram/dev/framework/view/wxml/event.html#touches)|
|changed_touches|list|Not None|说明见[小程序文档](https://developers.weixin.qq.com/miniprogram/dev/framework/view/wxml/event.html#touches)|

**Returns:**
- `None`

  
**代码示例:**

```python
import minium, time
class TestElement(minium.MiniTest):
    def test_touch_start_move_end(self):
        page = self.app.redirect_to("/pages/testelement/testelement")
        element = page.get_element("movable-view")
        rect = element.rect
        x_offset = 30 
        y_offset = 70
        move_delay = 350
        offset = element.offset
        size = element.size
        page_offset = element.page_offset
        changed_touch = touch = ori_changed_touch = ori_touch = {
            "identifier": 0,
            "pageX": offset["left"] + size["width"] // 2,
            "pageY": offset["top"] + size["height"] // 2,
            "clientX": offset["left"] + size["width"] // 2 - page_offset.x,
            "clientY": offset["top"] + size["height"] // 2 - page_offset.y,
        }
        element.touch_start(touches=[ori_touch], changed_touches=[ori_changed_touch])
        time.sleep(move_delay / 2000)
        changed_touch = touch = {
            "identifier": 0,
            "pageX": offset["left"] + size["width"] // 2 + x_offset,
            "pageY": offset["top"] + size["height"] // 2 + y_offset,
            "clientX": offset["left"] + size["width"] // 2 - page_offset.x + x_offset,
            "clientY": offset["top"] + size["height"] // 2 - page_offset.y + y_offset,
        }
        element.touch_move(touches=[touch], changed_touches=[changed_touch])
        time.sleep(move_delay / 2000)
        element.touch_end(changed_touches=[changed_touch])
        time.sleep(move_delay / 1000)
        self.assertDictEqual(
            {
                "left": rect["left"] + x_offset,
                "top": rect["top"] + y_offset,
                "width": rect["width"],
                "height": rect["height"],
            },
            element.rect,
        )
```

---

## touch_end() :id=touch_end
> 触发元素的 touchend事件

!> 公共库 2.9.1 之后生效

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|changed_touches|list|Not None|说明见[小程序文档](https://developers.weixin.qq.com/miniprogram/dev/framework/view/wxml/event.html#touches)|

**Returns:**
- `None`

**代码示例见[touch_move](#touch_move)**

---

## styles() :id=styles
> 获取元素的样式属性

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|names|str \| list |Not None|需要获取的 style 属性|


**Returns:**
- `List`

**代码示例:**

```python
import minium
@minium.ddt_class
class TestElement(minium.MiniTest):
    @classmethod
    def setUpClass(cls):
        super(TestElement, cls).setUpClass()
        cls.page = cls.app.redirect_to("/pages/testelement/testelement")

    @minium.ddt_case(
        ("color", "rgb(255, 0, 0)"),
        ("background-color", "rgb(0, 0, 255)"),
        (["color", "background-color"], ["rgb(255, 0, 0)", "rgb(0, 0, 255)"]),
    )
    def test_style(self, args):
        [names, values] = args
        e = self.page.get_element(".testattr")
        styles = e.styles(names)
        if len(styles) == 1:
            self.assertEqual(values, styles[0])
        else:
            self.assertListEqual(values, styles)
```

---