# 示例
小程序既不属于HTML5，也不属于传统的App，所以小程序的测试也会区别于传统的测试，下面我们给出几个不同场景的测试例子，更多例子请参考[小程序测试示例](https://git.weixin.qq.com/minitest/miniprogram-demo-test.git)
## UI测试示例

我们可以通过对元素的点击驱动小程序自动化。

```python
import time
import minium

class ComponentTest(minium.MiniTest):
    def test_ui_op(self):
        self.page.get_element("view", inner_text="视图容器").click()
        self.page.get_element(".navigator-text", inner_text="swiper").click()
        self.page.get_elements("switch")[0].click()
        self.page.get_elements("switch")[1].click()

```

## 单页面示例
一般的UI测试在前期的条件构建上面会花费很多时间，但是基于小程序的开发模式和我们的测试机接口，可以直接跳过中间页面，直接跳转到测试的页面。如：

```python
import time
import minium

class ComponentTest(minium.MiniTest):
    def test_set_data(self):
        self.app.navigate_to("/page/component/pages/text/text")
        self.page.data = {
            'text': "只能加文字，不能删除文字",
            'canAdd': True,
            'canRemove': False
        }
        time.sleep(1)
        self.capture("canAdd")
        self.page.data = {
            'text': "只能删除文字，不能加文字",
            'canAdd': False,
            'canRemove': True
        }
        time.sleep(1)
        self.capture("canRemove")
```

## 原生组件示例
小程序依赖于微信生态，有很多API涉及到微信内部的回调，对于这部分的UI操作我们提供一些函数封装。当然也可以参考[mock示例](#mock示例)。

```python
import minium

class NativeTest(minium.MiniTest):
    def test_modal(self):
        """
        操作原生modal实例
        """
        self.app.navigate_to("/page/API/pages/modal/modal")
        self.page.get_element("button", inner_text="有标题的modal").click()
        self.capture("有标题的modal")
        self.native.handle_modal("确定")
        self.page.get_element("button", inner_text="无标题的modal").click()
        self.capture("无标题的modal")
        self.native.handle_modal("取消")

```

## mock示例
现在市面上很多基于定位设计功能的小程序，利用mock函数，我们可以根据自定义的位置信息编写基于定位的用例。关于mock，请参考[mock函数](minium/Python/api/App.md#mock_wx_method)

```python
import minium

class MockTest(minium.MiniTest):

    def test_mock_location(self):
        self.app.navigate_to("/page/API/pages/get-location/get-location")

        # 模拟武汉光谷位置
        mock_location = {"latitude":30.5078502719,"longitude":114.4191741943,"speed":-1,"accuracy":65,"verticalAccuracy":65,"horizontalAccuracy":65,"errMsg":"getLocation:ok"}
        self.app.mock_wx_method("getLocation", result=mock_location)

        # 检查mock数据
        self.page.get_element("button", inner_text="获取位置").click()
        locations = self.page.get_element(".page-body-text-location").get_elements("text")
        self.assertEqual(locations[0].inner_text, "E: 114°42′", "经度校验")
        self.assertEqual(locations[1].inner_text, "N: 30°51′", "纬度校验")

        # 去掉mock
        self.app.restore_wx_method("getLocation")
        self.page.get_element("button", inner_text="获取位置").click()
        locations = self.page.get_element(".page-body-text-location").get_elements("text")
        self.assertNotEqual(locations[0].inner_text, "E: 114°42′", "经度校验")
        self.assertNotEqual(locations[1].inner_text, "N: 30°51′", "纬度校验")

```

## 接口测试示例

```python
import minium

class MockTest(minium.MiniTest):
    def test_mock_location(self):
        # 将 on_load()函数以 onLoad 为名字暴露给小程序
        self.app.expose_function("onLoad", self.on_load)
        # 将代码注入到 APPService hook 生命周期函数，在 this.onLoad() 被调用时，执行上面暴露的self.on_load()函数
        self.app.evaluate("function() {this.onLoad(function(options) {onLoad(options)})}")
        self.app.navigate_to("/page/component/pages/slider/slider")

    def on_load(self, message):
        # 注入 js 代码，测试小程序的接口
        print(message)
        self.app.evaluate(
            """function () {
                console.log(this.getCurrentPages().slice(-1)[0])
            }"""
        )
```

## input组件输入
因为input组件在小程序上是native实现的，三Android，iOS，IDE上各不相同，所以不能直接输入，我们可以调用trigger函数直接触发input组件绑定的函数，以实现输入的效果。

> 要注意的是，trigger函数直接触发了小程序在input组件上的函数，在UI上是看不到input组件有输入内容的。

```python
import time
import minium

class ComponentTest(minium.MiniTest):
    def test_input_element(self):
        self.app.navigate_to("pages/input/input")
        es = self.page.get_elements(".weui-input")
        # 直接触发事件，但是不会影响UI变化
        es[2].trigger("input", {"value": "测试以惺惺相惜"})
```

## 测试流程控制 & 数据驱动测试 :id=test_ddt
我们测试框架继承自unittest，所以unittest相关的工具我们都可以用，下面是我们集成数据驱动测试(基于ddt封装)的例子
```python
import minium

@minium.ddt_class
class BaseTest(minium.AssertBase):
    @minium.exit_when_error
    def test_init(self):
        """
        这条用例失败会退出测试计划，minium.exit_when_error可以用来修饰初始化用例
        """
        self.assertEqual(1, 1)
    
    @minium.ddt_case(1, 2, 3)
    def test_ddt(self, value):
        """
        数据驱动测试，这个case会自动展开成3条用例：
        test_ddt_1_1
        test_ddt_2_2
        test_ddt_3_3
        """
        self.assertIn(value, [1, 3])
```
你还能给具体的test data命名，自定义命名会体现在测试方法名中。使用`minium.ddt_class`修饰测试类时，带上`testNameFormat`参数设置格式化规则, 可配置项目包括:
- `%(index)s`    
- `%(value)s`    
- `%(name)s`

其中 `%(index)s` 必须存在，不配置则默认加上。

一些例子:

- 默认格式化后的测试方法名为：`%(testname)s_%(index)s_%(name)s_%(value)s`
- `testNameFormat="%(name)s"` 格式化后的测试方法名为：`%(testname)s_%(index)s_%(name)s`
- `testNameFormat="%(name)s_%(index)s"` 格式化后的测试方法名为：`%(testname)s_%(name)s_%(index)s`

```python
from minium import ddt_class, ddt_case, ddt_data, AssertBase

@ddt_class(testNameFormat="%(name)s")  
class RenameTest(AssertBase):
    @ddt_case(
        ddt_data(1, name="test_one"),
        ddt_data(2, name="test_two")
    )
    def test_ddt(self, value):
        """
        数据驱动测试，这个case会自动展开成2条用例：
        test_ddt_1_test_one
        test_ddt_2_test_two
        """
        self.assertIn(value, [1, 3])
```


?> 更多示例，请查看[小程序测试示例](https://git.weixin.qq.com/minitest/miniprogram-demo-test.git)
