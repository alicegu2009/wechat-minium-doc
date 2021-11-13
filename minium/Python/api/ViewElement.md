# `Class` minium.ViewElement :id=minium-view-element
> `ViewElement` 继承于  `BaseElement`, 提供了对视图类组件的操作 <br />
元素标签包含`scroll-view`, `swiper`, `movable-view`

!> 公共库 2.10.0 开始生效

通过调用[`Page.get_element`](minium/Python/api/Page?id=get_element)/[`Page.get_elements`](minium/Python/api/Page?id=get_elements)/[`Element.get_element`](minium/Python/api/Element?id=get_element)/[`Element.get_elements`](minium/Python/api/Element?id=get_elements)方法获取`Element`实例

---

## scroll_to() :id=scroll_to
> scroll-view 容器滚动操作


**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|  x |num|None|x 轴上滚动的距离|
|  y |num|None|y 轴上滚动的距离|

**Returns:**
- `None`

**代码示例:** 

```python
import minium, threading
@minium.ddt_class
class TestElement(minium.MiniTest):
    def test_scroll_to(self):
        page = self.app.redirect_to("/pages/testelement/testelement")
        callback_args = None
        callback_called = threading.Semaphore(0)
        def callback(args):
            nonlocal callback_args
            callback_args = args
            callback_called.release()
        self.app.hook_current_page_method("scroll", callback)
        el = page.get_element("scroll-view")
        el.scroll_to(x=20)
        self.assertTrue(callback_called.acquire(timeout=10), "callback called")
        self.assertEqual(callback_args[0]["detail"]["scrollLeft"], 20, "pick ok")
```

---

## swipe_to() :id=swipe_to
> 切换 swiper 容器当前的页面


**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|  index |int|None| 索引值，从 0 开始 |

**Returns:**
- `None`

**代码示例:** 

```python
import minium, threading
@minium.ddt_class
class TestElement(minium.MiniTest):
    def test_swipe_to(self):
        page = self.app.redirect_to("/pages/testelement/testelement")
        callback_args = None
        callback_called = threading.Semaphore(0)
        def callback(args):
            nonlocal callback_args
            callback_args = args
            callback_called.release()
        self.app.hook_current_page_method("swiperChange", callback)
        el = page.get_element("swiper")
        el.swipe_to(2)
        self.assertTrue(callback_called.acquire(timeout=10), "callback called")
        self.assertEqual(callback_args[0]["detail"]["current"], 2, "pick ok")
```

---

## move_to() :id=move_to
> movable-view 容器拖拽滑动


**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|  x |num|None|x 轴方向的偏移距离|
|  y |num|None|y 轴方向的偏移距离|

*PS: x,y 偏移量相对于`movable-area`左上角，如示例中，`movable-area`左上角为(25, 25)*

**Returns:**
- `None`

**代码示例:** 

```python
import minium, time
@minium.ddt_class
class TestElement(minium.MiniTest):
    def test_move_to(self):
        page = self.app.redirect_to("/pages/testelement/testelement")
        element = page.get_element("movable-view")
        # reset
        element.move_to(0, 0)
        time.sleep(2)
        rect = element.rect # 记录初始位置
        print(rect)
        element.move_to(20, 100)
        time.sleep(2)
        self.assertDictContainsSubset(
            {
                "left": 20 + rect.x,
                "top": 100 + rect.y,
            },
            element.rect,
        )
```

---