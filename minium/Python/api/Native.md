# `Class` minium.Native :id=minium-native

> `Native` 提供了针对小程序内涉及原生控件的操作封装

!> 以下部分接口开发者工具模拟器暂时还不支持

可以通过实例化[`minium.Native`](#init)或从[`minium.MiniTest.native`](minium/Python/framework/Minitest)获得`Native`实例

---

## Native() :id=init
> 初始化

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|json_conf|dict|Not None|native 操作初始化参数, 对应[测试配置](minium/Python/framework/config.md)中`device_desire`字段|
|platform|str|Not None|平台。目前仅支持`android`和`ios`|

Android 和 iOS 使用的 json_conf 有些许差别：

*Android json_conf ：*

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|serial|str|Not None|手机 serial 序列号|

*iOS json_conf ：*

参考[`IOS的device_desire配置项`](minium/Python/framework/config.md#IOS的device_desire配置项)

**代码示例:** 

```python
import minium
native = minium.Native({
    "serial": "28fb61d0ef1c7ece"
}, "android")

```

---

## start_wechat() :id=start_wechat
> 启动微信


**Returns:**
- `None`

**代码示例:** 

```python
import minium
class NativeTest(minium.MiniTest):
    def test_start_wechat(self):
        self.native.start_wechat()
	
```
---

## stop_wechat() :id=stop_wechat
> 杀掉微信


**Returns:**
- `None`

**代码示例:** 

```python
import minium
class NativeTest(minium.MiniTest):
    def test_start_wechat(self):
        self.native.stop_wechat()	
```
---

## screen_shot() :id=screen_shot
> 截屏

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|filename|str|时间戳|截图文件名|

**Returns:**

- 截图文件名 `str`

**代码示例:** 

```python
import minium, os
class NativeTest(minium.MiniTest):
    def test_screen_shot(self):
        file_name = "test_native_screen_shot.png"
        if os.path.isfile(file_name):
            os.remove(file_name)
        self.native.screen_shot(file_name)
        self.assertTrue(os.path.isfile(file_name), "%s exists" % file_name)
        os.remove(file_name)
```
---

## allow_login() :id=allow_login
> 处理微信登陆确认弹框，点击允许或者取消

同[`allow_authorize`](#allow_authorize)

---

## allow_authorize() :id=allow_authorize
> 处理微信授权弹框的通用方法，点击允许或者拒绝。
> 该方法不对具体授权窗口做检验工作

> 可处理窗口包括：获取用户信息，获取位置，获取微信运动数据，获取录音权限，获取相册权限，获取摄像头权限，获取手机号码

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|answer|bool|True|True 或 False|

**Returns:**
- `bool`

```python
import minium, threading
@minium.ddt_class(testNameFormat="%(name)s_%(index)s")
class TestNative(minium.MiniTest):
    @classmethod
    def setUpClass(cls) -> None:
        super().setUpClass()
        cls.mini.clear_auth()   # 先清空授权才会弹窗
    
    def setUp(self) -> None:
        super().setUp()
        if self.page.path != "/pages/testnative/testnative":
            self.app.redirect_to("/pages/testnative/testnative")

    @minium.ddt_unpack
    @minium.ddt_case(
        ddt_data(("record", "allow_record"), "record"),
        ddt_data(("writePhotosAlbum", "allow_write_photos_album"), "write_photos_album"),
        ddt_data(("camera", "allow_camera"), "camera"),
    )
    def test_allow_authorize(self, scope, method):
        called = threading.Semaphore(0)
        callback_args = None

        def callback(args):
            nonlocal callback_args
            called.release()
            callback_args = args

        self.app.hook_wx_method("authorize", callback=callback)
        self.app.get_current_page().get_element(f"#{scope}").tap()
        getattr(self.native, method)()
        self.assertTrue(called.acquire(timeout=10), "callback called")
        self.assertDictContainsSubset({"errMsg": "authorize:ok"}, callback_args[0])
```

---


## allow_get_user_info() :id=allow_get_user_info
> 处理获取用户信息确认弹框

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|answer|bool|True|True 或 False|

**Returns:**
- `None`

**代码示例:** 

```python
import minium, threading
@minium.ddt_class(testNameFormat="%(name)s_%(index)s")
class TestNative(minium.MiniTest):
    @classmethod
    def setUpClass(cls) -> None:
        super().setUpClass()
        cls.mini.clear_auth()   # 先清空授权才会弹窗
    
    def setUp(self) -> None:
        super().setUp()
        if self.page.path != "/pages/testnative/testnative":
            self.app.redirect_to("/pages/testnative/testnative")

    def test_allow_get_user_info(self):
        called = threading.Semaphore(0)
        callback_args = None

        def callback(args):
            nonlocal callback_args
            callback_args = args
            called.release()

        self.app.hook_wx_method("getUserProfile", callback=callback)
        self.app.get_current_page().get_element("#getUserProfile").tap()
        self.native.allow_get_user_info()
        self.assertTrue(called.acquire(timeout=10), "callback called")
        self.assertDictContainsSubset({"errMsg": "getUserProfile:ok"}, callback_args[0])
```
---

## allow_get_location() :id=allow_get_location
> 处理获取位置信息确认弹框

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|answer|bool|True|True 或 False|


**Returns:**
- `None`

**代码示例:** 

```python
import minium, threading
@minium.ddt_class(testNameFormat="%(name)s_%(index)s")
class TestNative(minium.MiniTest):
    @classmethod
    def setUpClass(cls) -> None:
        super().setUpClass()
        cls.mini.clear_auth()   # 先清空授权才会弹窗
    
    def setUp(self) -> None:
        super().setUp()
        if self.page.path != "/pages/testnative/testnative":
            self.app.redirect_to("/pages/testnative/testnative")

    def test_allow_get_location(self):
        called = threading.Semaphore(0)
        callback_args = None

        def callback(args):
            nonlocal callback_args
            callback_args = args
            called.release()

        self.app.hook_wx_method("getLocation", callback=callback)
        self.app.get_current_page().get_element("#testGetLocation").tap()
        self.native.allow_get_location()
        self.assertTrue(called.acquire(timeout=10), "callback called")
        # getLocation:fail:ERROR_SERVER_NOT_LOCATION : 手机使用5g可能会报这个错误，但不是接口问题，pass
        if callback_args[0]["errMsg"] == "getLocation:fail:ERROR_SERVER_NOT_LOCATION":
            self.logger.error("getLocation:fail:ERROR_SERVER_NOT_LOCATION")
            return
        self.assertDictContainsSubset({"errMsg": "getLocation:ok"}, callback_args[0])
```
---

## allow_get_we_run_data() :id=allow_get_we_run_data
> 处理获取微信运动数据确认弹框

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|answer|bool|True|True 或 False|

**Returns:**
- `None`

**代码示例:** 

```python
import minium, threading
@minium.ddt_class(testNameFormat="%(name)s_%(index)s")
class TestNative(minium.MiniTest):
    @classmethod
    def setUpClass(cls) -> None:
        super().setUpClass()
        cls.mini.clear_auth()   # 先清空授权才会弹窗
    
    def setUp(self) -> None:
        super().setUp()
        if self.page.path != "/pages/testnative/testnative":
            self.app.redirect_to("/pages/testnative/testnative")

    def test_allow_get_we_run_data(self):
        except_fail = False
        try:
            ret = self.app.call_wx_method("getSetting", [{}]).get("result", {}).get("result")
            authSetting = ret.get("authSetting", None)
            if authSetting and authSetting.get("scope.werun") == False:
                except_fail = True
        except:
            except_fail = True
        called = threading.Semaphore(0)
        ret = {"errMsg": ""}

        def callback(args):
            ret["errMsg"] = args[0].get("errMsg")
            called.release()

        self.app.hook_wx_method("getWeRunData", callback=callback)
        self.app.get_current_page().get_element("#testGetWeRunData").tap()
        print("before allow %f" % time.time())
        self.native.allow_get_we_run_data()
        print("after allow %f" % time.time())
        self.assertTrue(called.acquire(timeout=30), "callback called")
        if except_fail:
            self.assertIn("getWeRunData:fail", ret["errMsg"])
        else:
            self.assertDictContainsSubset({"errMsg": "getWeRunData:ok"}, ret)
```

---

## allow_record() :id=allow_record
> 处理获取录音权限确认弹框

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|answer|bool|True|True 或 False|


**Returns:**
- `None`

**代码示例见[`allow_authorize`](allow_authorize)** 

---

## allow_write_photos_album() :id=allow_write_photos_album
> 处理获取保存相册确认弹框

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|answer|bool|True|True 或 False|


**Returns:**
- `None`

**代码示例见[`allow_authorize`](allow_authorize)** 

---

## allow_camera() :id=allow_camera
> 处理使用摄像头确认弹框

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|answer|bool|True|True 或 False|


**Returns:**
- `None`

**代码示例见[`allow_authorize`](allow_authorize)** 

---

## allow_get_user_phone() :id=allow_get_user_phone
> 处理获取用户手机号码确认弹框

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|answer|bool|True|True 或 False|


**Returns:**
- `None`

**代码示例:** 

```python
import minium, threading
@minium.ddt_class(testNameFormat="%(name)s_%(index)s")
class TestNative(minium.MiniTest):
    @classmethod
    def setUpClass(cls) -> None:
        super().setUpClass()
        cls.mini.clear_auth()   # 先清空授权才会弹窗
    
    def setUp(self) -> None:
        super().setUp()
        if self.page.path != "/pages/testnative/testnative":
            self.app.redirect_to("/pages/testnative/testnative")

    def test_allow_get_user_phone(self):
        """
        测试号可能没有绑定手机
        """
        called = threading.Semaphore(0)
        detail = errMsg = None

        def callback(args):
            nonlocal detail, errMsg
            called.release()
            detail = args[0]["detail"]
            errMsg = detail["errMsg"]

        self.app.hook_current_page_method("testGetPhoneint", callback)
        self.app.get_current_page().get_element("#testGetPhoneint").tap()
        if self.native.handle_modal("取消", title="微信帐号还没有绑定手机号"):  # 没绑定，会回调fail
            self.assertTrue(called.acquire(timeout=10), "callback called")
            self.assertIn("getPhoneint:fail", errMsg)
        else:  # 有绑定，需要点击弹窗
            self.native.allow_get_user_phone()
            self.assertTrue(called.acquire(timeout=10), "callback called")
            self.assertDictContainsSubset({"errMsg": "getPhoneint:ok"}, detail)
```

---

## handle_modal() :id=handle_modal
> 处理模态弹窗

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|btn_text|str|确定|根据传入的 name 进行点击|
|title|str|None|传入弹窗的 title 可以校验当前弹窗是否为预期弹窗|


**Returns:**
- `None`

**代码示例:** 

```python
import minium, threading, time
@minium.ddt_class(testNameFormat="%(name)s_%(index)s")
class TestNative(minium.MiniTest):    
    def setUp(self) -> None:
        super().setUp()
        if self.page.path != "/pages/testnative/testnative":
            self.app.redirect_to("/pages/testnative/testnative")

    def test_handle_modal(self):
        called = threading.Semaphore(0)
        callback_args = None

        def callback(args):
            nonlocal callback_args
            called.release()
            callback_args = args
            
        self.app.hook_wx_method("showModal", callback=callback)
        self.page.get_element("#testModal").tap()
        time.sleep(2)
        self.native.handle_modal("确定")
        is_called = called.acquire(timeout=10)
        self.app.release_hook_wx_method("showModal")
        self.assertTrue(is_called, "callback called")
        self.assertDictContainsSubset({"errMsg": "showModal:ok", "cancel": False, "confirm": True}, callback_args[0])
```

---

## handle_action_sheet() :id=handle_action_sheet
!> 推荐使用[FormElement.pick()](/minium/Python/api/FormElement?id=pick)
> 处理上拉菜单

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|item|str|Not None|要选择的 item|

**Returns:**
- `None`

**代码示例:** 

```python
import minium, threading, time
@minium.ddt_class(testNameFormat="%(name)s_%(index)s")
class TestNative(minium.MiniTest):    
    def setUp(self) -> None:
        super().setUp()
        if self.page.path != "/pages/testnative/testnative":
            self.app.redirect_to("/pages/testnative/testnative")

    @minium.ddt_case(
        ("A", {"errMsg": "showActionSheet:ok", "tapIndex": 0}),
        ("测试", {"errMsg": "showActionSheet:ok", "tapIndex": 1}),
        ("C", {"errMsg": "showActionSheet:ok", "tapIndex": 2}),
        ("取消", {"errMsg": "showActionSheet:fail cancel"}),
    )
    def test_handle_action_sheet(self, args):
        [item, cb_args] = args
        called = threading.Semaphore(0)
        callback_args = None

        def callback(args):
            nonlocal callback_args
            called.release()
            callback_args = args
        self.app.hook_wx_method("showActionSheet", callback=callback)
        self.page.get_element("#testActionSheet").tap()
        time.sleep(1)
        self.native.handle_action_sheet(item)
        is_called = called.acquire(timeout=10)
        self.app.release_hook_wx_method("showActionSheet")
        self.assertTrue(is_called, "callback called")
        self.assertDictEqual(cb_args, callback_args[0])
```
---

## forward_miniprogram() :id=forward_miniprogram
> 通过右上角更多菜单转发小程序

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|name|str|Not None|要分享的人|
|text|str|None|分享携带的内容|
|create_new_chat|bool|True|是否新建聊天|

**Returns:**
- `None`

**代码示例:** 

```python
import minium
class TestNative(minium.MiniTest):
    def test_forward_miniprogram(self):
        if self.page.path != "/pages/testnative/testnative":
            self.app.redirect_to("/pages/testnative/testnative")
        self.native.forward_miniprogram("微信团队", "转发发送的文字")
```
---

## forward_miniprogram_inside() :id=forward_miniprogram_inside
> 小程序内触发转发小程序

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|name|str|Not None|要分享的人|
|text|str|None|分享携带的内容|
|create_new_chat|bool|True|是否新建聊天|

**Returns:**
- `None`

**代码示例:** 

```python
import minium, time
class TestNative(minium.MiniTest):
    def test_forward_miniprogram_inside(self):
        if self.page.path != "/pages/testnative/testnative":
            self.app.redirect_to("/pages/testnative/testnative")
        self.page.get_element("#testshare").tap()
        time.sleep(2)
        self.native.forward_miniprogram_inside("微信团队", "转发发送的文字")
```
---

## input_text() :id=input_text
!> 接口将不再维护。推荐使用[FormElement.input()](/minium/Python/api/FormElement?id=input)
> 给当前获得焦点的 input 输入框输入文字

!> 调用此函数之前必须确保输入框处于输入状态, 部分安卓机器已经不适用
某些 wda 版本密码框输入会失败

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|text|str|None|输入的内容|


**Returns:**
- `None`

**代码示例:** 

```python
import minium, time
class TestNative(minium.MiniTest):    
    def setUp(self) -> None:
        super().setUp()
        if self.page.path != "/pages/testnative/testnative":
            self.app.redirect_to("/pages/testnative/testnative")

    def test_input_text(self):
        page = self.page
        el = page.get_element("input")
        page.scroll_to(el.offset.y)
        time.sleep(1)
        el.click()
        time.sleep(1)
        self.native.input_text("testdata")
        time.sleep(1)
        self.page.get_element("#empty").click()
```

---

## input_clear() :id=input_clear
!> 推荐使用[FormElement.input()](/minium/Python/api/FormElement?id=input)
> 清除当前获得焦点的 input 输入框的文字

!> 调用此函数之前必须确保输入框处于输入状态, 部分安卓机器已经不适用

**Parameters:**
- `None`

**Returns:**
- `None`

**代码示例:** 

```python
import minium, time
class TestNative(minium.MiniTest):    
    def setUp(self) -> None:
        super().setUp()
        if self.page.path != "/pages/testnative/testnative":
            self.app.redirect_to("/pages/testnative/testnative")

    def test_input_text(self):
        page = self.page
        el = page.get_element("input")
        page.scroll_to(el.offset.y)
        time.sleep(1)
        el.click()
        time.sleep(1)
        self.native.input_clear()
        time.sleep(1)
        self.page.get_element("#empty").click()
```

---

## textarea_text() :id=textarea_text
!> 推荐使用[FormElement.input()](/minium/Python/api/FormElement?id=input)
> 给当前获得焦点的 textarea 输入框输入文字, 部分安卓机器已经不适用

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|text|str|None|输入的内容|
|index|int|0|多个 textarea 同时存在一个页面从上往下排序, 计数从 0 开始|


**Returns:**
- `None`

**代码示例:** 

```python
import minium, time
class TestNative(minium.MiniTest):    
    def setUp(self) -> None:
        super().setUp()
        if self.page.path != "/pages/testnative/testnative":
            self.app.redirect_to("/pages/testnative/testnative")

    def test_input_text(self):
        page = self.page
        el = page.get_element("textarea")
        page.scroll_to(el.offset.y)
        time.sleep(1)
        el.click()
        time.sleep(1)
        self.native.input_text('中关村科技园，起源于20世纪80年代初的“中关村电子一条街”，是中国第一个国家级高新技术产业开发区、\
        第一个国家自主创新示范区、第一个国家级人才特区，也是京津石高新技术产业带的核心园区。中关村科技园是我国体制机制创新的试验田，被誉为“中国硅谷”。')
        time.sleep(1)
        self.page.get_element("#empty").click()
```

---

## textarea_clear() :id=textarea_clear
!> 推荐使用[FormElement.input()](/minium/Python/api/FormElement?id=input)
> 清楚当前获得焦点的 textarea 输入框的文字, 部分安卓机器已经不适用

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|index|int|0|多个 textarea 同时存在一个页面从上往下排序, 计数从 0 开始|


**Returns:**
- `None`

**代码示例:** 

```python
import minium, time
class TestNative(minium.MiniTest):    
    def setUp(self) -> None:
        super().setUp()
        if self.page.path != "/pages/testnative/testnative":
            self.app.redirect_to("/pages/testnative/testnative")

    def test_input_text(self):
        page = self.page
        el = page.get_element("textarea")
        page.scroll_to(el.offset.y)
        time.sleep(1)
        el.click()
        time.sleep(1)
        self.native.textarea_clear()
        time.sleep(1)
        self.page.get_element("#empty").click()
```
---

## map_select_location() :id=map_select_location
> 原生地图组件选择位置

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|name|str|None|位置名称|


**Returns:**
- `None`

**代码示例:** 

```python
import minium, threading
@minium.ddt_class(testNameFormat="%(name)s_%(index)s")
class TestNative(minium.MiniTest):
    @classmethod
    def setUpClass(cls) -> None:
        super().setUpClass()
        cls.mini.clear_auth()   # 先清空授权才会弹窗
    
    def setUp(self) -> None:
        super().setUp()
        if self.page.path != "/pages/testnative/testnative":
            self.app.redirect_to("/pages/testnative/testnative")

    def test_map_select_location(self):
        called = threading.Semaphore(0)
        callback_args = None

        def callback(args):
            nonlocal callback_args
            called.release()
            callback_args = args
        self.app.hook_wx_method("chooseLocation", callback=callback)
        self.page.get_element("#testChooseLocation").tap()
        time.sleep(1)
        self.native.allow_get_location(True)  # 授权获取位置
        self.native.map_select_location("腾讯微信总部")  # 确认选择位置
        expected_res = {
            "errMsg": "chooseLocation:ok",
            "name": "腾讯微信总部",
            "address": "广东省广州市海珠区tit创意园品牌街",
            "latitude": 23.100039,
            "longitude": 113.32456,
        }
        is_called = called.acquire(timeout=10)
        self.app.release_hook_wx_method("chooseLocation")
        self.assertTrue(is_called, "callback called")
        self.assertDictContainsSubset(expected_res, callback_args[0])
```

---

## map_back_to_mp() :id=map_back_to_mp
> 原生地图组件查看定位页面,返回小程序

**Parameters:**
- `None`


**Returns:**
- `None`

**代码示例:** 

```python
import minium, threading
@minium.ddt_class(testNameFormat="%(name)s_%(index)s")
class TestNative(minium.MiniTest):
    @classmethod
    def setUpClass(cls) -> None:
        super().setUpClass()
        cls.mini.clear_auth()   # 先清空授权才会弹窗
    
    def setUp(self) -> None:
        super().setUp()
        if self.page.path != "/pages/testnative/testnative":
            self.app.redirect_to("/pages/testnative/testnative")

    def test_map_back_to_mp(self):
        called = threading.Semaphore(0)
        callback_args = None

        def callback(args):
            nonlocal callback_args
            called.release()
            callback_args = args
        self.app.hook_wx_method("chooseLocation", callback=callback)
        self.app.get_current_page().get_element("#testChooseLocation").tap()
        time.sleep(1)
        self.native.allow_get_location(True)  # 授权获取位置
        self.native.map_back_to_mp()  # 确认选择位置
        is_called = called.acquire(timeout=10)
        self.app.release_hook_wx_method("chooseLocation")
        self.assertTrue(is_called, "callback called")
        self.assertDictContainsSubset({"errMsg": "chooseLocation:fail cancel"}, callback_args[0])
```
---

## deactivate() :id=deactivate
> 使微信进入后台一段时间, 再切回前台

!> iOS 12.0 之后由于 wda 的原因可能不生效 

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|duration|int|None|进入后台的时间|


**Returns:**
- `None`

**代码示例:** 

```python
import minium
class NativeTest(minium.MiniTest):
    def test_deactivate(self):
        self.native.deactivate(10)
```

---
## get_pay_value() :id=get_pay_value

!> IOS因为隐私政策，所以只支持 Android

> 获取支付弹窗中的支付金额

**Parameters:**

- `None`

**Returns:**

- `str`

---

## input_pay_password() :id=input_pay_password

!> IOS因为隐私政策，所以只支持 Android

> 支付弹窗中输入支付密码

**Parameters:**

| 名称 |  类型  | 默认值 | 说明     |
| :--- | :----: | :----: | :------- |
| psw  | str |   无   | 支付密码 |

**Returns:**

- `None`

---

## close_payment_dialog() :id=close_payment_dialog

> 关闭支付弹窗

**Parameters:**

- `None`

**Returns:**

- `None`

---

## text_exists() :id=text_exists

> 检测原生页面上文字是否存在

**Parameters:**

| 名称         |  类型   | 默认值 | 说明                                                |
| :----------- | :-----: | :----: | :-------------------------------------------------- |
| text         | str  |  None  | 需要检测的文字                                      |
| iscontain    | bool | False  | 为False时，完全匹配文本<br />为True时，包含匹配文本 |
| wait_seconds | int  |   5    | 最小等待市场，单位s                                 |

**Returns:**

- `bool`

**代码示例:** 

```python
import minium, time
class TestNative(minium.MiniTest):    
    def setUp(self) -> None:
        super().setUp()
        if self.page.path != "/pages/testnative/testnative":
            self.app.redirect_to("/pages/testnative/testnative")

    def test_text_exists(self):
        self.page.get_element("#testModal").tap()
        time.sleep(2)
        self.assertTrue(self.native.text_exists("modal", iscontain=True))
        self.assertFalse(self.native.text_exists("modal"))
        self.native.handle_modal("确定")
```

------

## text_click() :id=text_click

> 点击原生页面上的文字

**Parameters:**

| 名称      |  类型   | 默认值 | 说明                                                |
| :-------- | :-----: | :----: | :-------------------------------------------------- |
| text      | str  |  None  | 需要点击的文字                                      |
| iscontain | bool | False  | 为False时，完全匹配文本<br />为True时，包含匹配文本 |

**Returns:**

- `None`

**代码示例:** 

```python
import minium, time, threading
class TestNative(minium.MiniTest):    
    def setUp(self) -> None:
        super().setUp()
        if self.page.path != "/pages/testnative/testnative":
            self.app.redirect_to("/pages/testnative/testnative")

    def test_text_click(self):
        called = threading.Semaphore(0)
        callback_args = None

        def callback(args):
            nonlocal callback_args
            called.release()
            callback_args = args
            
        self.app.hook_wx_method("showModal", callback=callback)
        self.page.get_element("#testModal").tap()
        time.sleep(2)
        self.native.text_click("确定")
        is_called = called.acquire(timeout=10)
        self.app.release_hook_wx_method("showModal")
        self.assertTrue(is_called, "callback called")
        self.assertDictContainsSubset({"errMsg": "showModal:ok", "cancel": False, "confirm": True}, callback_args[0])
```

------

## start_get_perf() :id=start_get_perf

> 获取 CPU 内存 数据

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|timeinterval|int|15|时间间隔，单位s|


**Returns:**
- `None`

**代码示例:** 

```python
import minium, time, json
class TestNative(minium.MiniTest):    
    def setUp(self) -> None:
        super().setUp()
        if self.page.path != "/pages/testnative/testnative":
            self.app.redirect_to("/pages/testnative/testnative")

    def test_get_perf(self):
        self.native.start_get_perf(1)
        time.sleep(20)
        data = json.loads(self.native.stop_get_perf())
        self.assertTrue(len(data) > 0, "have data")
        item = data[int(len(data)/2)]
        self.assertNotEqual(item["cpu"], 0, "cpu not 0")
        self.assertNotEqual(item["mem"], 0, "mem not 0")
```

---

## stop_get_perf() :id=stop_get_perf

> 获取 CPU 内存 数据

**Parameters:**

- `None`

**Returns:**
- `str`: [{"cpu": cpu_data, "mem": mem_data, "timestamp": timestamp}, ... ,]

**代码示例见[start_get_perf](#start_get_perf)** 

---

## start_get_fps() :id=start_get_fps
!> 该接口废除，请使用`start_get_perf`和`stop_get_perf`

---

## click_coordinate() :id=click_coordinate
> 点击坐标

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|x|int||横坐标|
|y|int||纵坐标|


**Returns:**
- `None`
