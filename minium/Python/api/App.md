# `Class` minium.App :id=minium-App

> `App` 提供小程序应用层面的各种操作, 包括页面跳转, 获取当前页面, 页面栈等功能

可以从[`minium.Minium.app`](minium/Python/api/Minium)或从[`minium.MiniTest.app`](minium/Python/framework/Minitest)获得`App`实例

**Properties:**

|名称| 类型| 默认值| 说明|
| :----- | ----- | :-----: | :----- |
|app_id|str|None|当前项目appid|

---

## screen_shot() :id=screen_shot
>  截图

!> ide上仅能截取到wxml页面的内容，Modal/Actionsheet/授权弹窗等无法截取

!> 公共库 2.10.0 开始生效

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|save_path|str|Not None| 截图保存路径|
|format|str|raw| 截图数据返回格式，raw 或者 pillow|

**Returns:**
-   raw or pillow data

**代码示例:**

``` python
#!/usr/bin/env python3
import minium, os
class AppTest(minium.MiniTest):
	def test_screen_shot(self):
        output_path = os.path.join(os.path.dirname(__file__), "outputs/test_screen_shot.png")
        if not os.path.isdir(os.path.dirname(output_path)):
            os.mkdir(os.path.dirname(output_path))
        if os.path.isfile(output_path):
            os.remove(output_path)
        ret = self.app.screen_shot(output_path)
        self.assertTrue(os.path.isfile(output_path))
        os.remove(output_path)
```


<!-- --- 该接口在case中没什么实际意义，去掉

## exit() :id=exit
> 退出小程序

!> 基础库 2.7.6 开始支持

**Returns:**
- None

**代码示例:**

``` python
#!/usr/bin/env python3
import minium
class AppTest(minium.MiniTest):
	def test_exit(self):
        self.app.exit()
``` -->

---

## enable_log() :id=enable_log
> 启动小程序日志事件

**Returns:**
- None

**代码示例:**
```python
import minium, threading
class TestApp(minium.MiniTest):
    def test_enable_log(self):
        has_log = threading.Semaphore(0)
        func_name = None
        def check_log(message):
            self.logger.info("type: {}, value: {}".format(str(type(message)), message))
            if len(message["args"]) == 1 and message["args"][0] == "test %s" % func_name:
                has_log.release()
        
        func_name = "add_observer"
        self.app.evaluate('function(){console.log("test %s")}' % func_name, sync=True)
        self.assertFalse(has_log.acquire(timeout=10), "绑定监听函数前不应有回调")
        self.app.add_observer("App.logAdded", check_log)
        self.app.enable_log()
        func_name = "enable_log"
        self.app.evaluate('function(){console.log("test %s")}' % func_name, sync=True)
        self.app.remove_observer("App.logAdded", check_log)
        self.assertTrue(has_log.acquire(timeout=10), "监听到日志")
        func_name = "remove_observer"
        self.app.evaluate('function(){console.log("test %s")}' % func_name, sync=True)
        self.assertFalse(has_log.acquire(timeout=10), "移除监听函数后不应有回调")
```

---

## add_observer() :id=add_observer
> 监听小程序的事件

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|event|str|Not None|需要监听的事件|
|callback|func|Not None|收到事件后的回调函数|

**Returns:**
- None

**代码示例见[`enable_log`](#enable_log)**

---

## remove_observer() :id=remove_observer
> 移除小程序事件监听

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|event|str|Not None|需要监听的事件|
|callback|func|None|对应[`add_observer`](#add_observer)指定的监听函数，不传则移除该事件所有监听函数|

**Returns:**
- None

**代码示例见[`enable_log`](#enable_log)**

---

## evaluate() :id=evaluate
> 向 app Service 层注入代码并执行

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|app_function|str|Not None|代码字符串|
|args|list|Not None|参数|
|sync|bool|False|是否同步执行|

**Returns:**

- sync == True: dict(result={"result": 函数返回值})
- sync == False: str(消息ID)。配合[`get_async_response`](#get_async_response)使用获取返回值

**代码示例:**
```python
import minium

# sync == True
@minium.ddt_class
class TestApp(minium.MiniTest):
    @minium.ddt_case([], ["1", "2"])
    def test_evaluate_sync(self, args):
        result = self.app.evaluate(
            "function(...args){return `test evaluate: ${args}`}", args, sync=True
        )
        self.assertEqual(
            result.get("result", {}).get("result"), "test evaluate: {}".format(",".join(args))
        )
```

---

## get_async_response() :id=get_async_response
> 获取`evaluate`方法异步调用的结果

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|msg_id|str|Not None|`evaluate`返回的消息ID|
|timeout|int|None|等待超时时间，None: 立刻返回|

**Returns:**
- None or Dict

**代码示例:**
```python
import minium

# sync == False
@minium.ddt_class
class TestApp(minium.MiniTest):
    @minium.ddt_case([], ["1", "2"])
    def test_evaluate_async(self, args):
        msg_id = self.app.evaluate(
            "function(...args){return `test evaluate: ${args}`}", args, sync=False
        )
        result = self.app.get_async_response(msg_id, 5)
        self.assertEqual(
            result.get("result", {}).get("result"), "test evaluate: {}".format(",".join(args))
        )
```

---

## expose_function() :id=expose_function
> 在 AppService 全局暴露方法，供小程序侧调用测试脚本中的方法，类似在开发者工具console pannel中运行代码

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|name|str|Not None|供小程序调用的方法名字|
|binding_function|list|Not None|脚本中的方法实现|

**Returns:**

- dict(name=f"{name}", args=[调用方法的参数])

**代码示例:**

``` python
#!/usr/bin/env python3
import minium
class AppTest(minium.MiniTest):
    def test_expose_function(self):
        is_callback = threading.Semaphore(0)
        expected_args = [1,2]
        actual_args = []

        def binding_func(message):
            nonlocal actual_args
            is_callback.release()
            actual_args = message.args

        self.app.expose_function("test_expose_function", binding_func)
        self.app.evaluate("function(...args){test_expose_function(...args)}", expected_args)
        self.assertTrue(is_callback.acquire(timeout=10), "有回调")
        self.assertListEqual(actual_args, expected_args, "回参一致")
```

---

## on_exception_thrown() :id=on_exception_thrown
> 监听小程序 js 层的错误，报错的时候执行回调

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|func|function|Not None|错误回调|

**Returns:**

- {"message": err_msg, "stack": err_stack}

**代码示例:**

```python
#!/usr/bin/env python3
import minium
class AppTest(minium.MiniTest):
	def test_on_exception_thrown(self):
        is_exception_thrown = threading.Semaphore(0)
        e = None
        def on_exception(err):
            nonlocal e
            is_exception_thrown.release()
            e = err

        self.app.on_exception_thrown(on_exception)
        try:
            self.app.navigate_to("/pages/exception_page/exception_page")
        except:
            pass
        self.assertTrue(is_exception_thrown.acquire(timeout=10), "监听到报错")
        self.assertTrue(is_exception_thrown.acquire(timeout=10), "监听到第二次报错")
        self.assertEqual(e.message, "thisonload is not defined")
        self.assertIsNotNone(e.stack, "stack not empty")
```

---

## call_wx_method() :id=call_wx_method
> 调用[小程序的API](https://developers.weixin.qq.com/miniprogram/dev/api/)

!> 非`wx.xxxSync`函数都会等到complete回调之后返回

**Parameters:**

!> 无需传递 success 与 fail 回调，fail 时直接当成调用错误

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|method|str|Not None|函数名|
|args|dict|None|参数|
|plugin_appid|str|"auto"|`"auto"`: 如果当前page是插件页，自动填充appid; <br>`"{appid}"`: 调用该插件环境下的wx.{method}; <br>`None`: 调用小程序环境下的wx.{method}|

**Returns:**

- dict(result={"result": success回调对象})

**代码示例:**
```python
#!/usr/bin/env python3
import minium
class AppTest(minium.MiniTest):
	def test_call_wx_method(self):
        sys_info = self.app.call_wx_method("getSystemInfo").get("result", {}).get("result")
        self.assertIsInstance(sys_info, dict, "is dict")
        self.assertTrue(True if sys_info else False, "not empty")
```

---

## mock_wx_method() :id=mock_wx_method
> mock掉小程序API的调用，能mock的函数参考[小程序的API](https://developers.weixin.qq.com/miniprogram/dev/api/)。


**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|method|str|Not None|函数名|
|functionDeclaration|str|None|functionDeclaration函数名|
|result|str\|dict|None|mock之后回调的结果|
|args|str\|dict|None|functionDeclaration函数的参数，functionDeclaration不为None时生效|
|success|bool|True| 触发回调类型，success or fail，result 为 str 时生效|
|plugin_appid|str|"auto"|`"auto"`: 如果当前page是插件页，自动填充appid; <br>`"{appid}"`: mock该插件环境下的wx.{method}; <br>`None`: mock小程序环境下的wx.{method}|

> 输入的 result 为 dict 时，相当于自定义一个完整的 wx method 返回结果，有比较高的自由度

**Returns:**
- None

**代码实例:**
```python
#!/usr/bin/env python3
import minium
@minium.ddt_class(testNameFormat="%(name)s_%(index)s")
class AppTest(minium.MiniTest):
	@minium.ddt_unpack
    @minium.ddt_case(
        # mock result
        ("getStorage", None, None, {"data": "test test test"}),
        # mock function and match key
        ("getStorage", """function(options,defval){
		    if(options.key==='test_mock') return {data:'1111'}
		    if(options.key==='sex') return {data:'male'}
		    return {data:defval}
		  }""", "unknow", {"data": "1111"}),
        # mock function and not match key
        ("getStorage", """function(options,defval){
		    if(options.key==='name') return {data:'1111'}
		    if(options.key==='sex') return {data:'male'}
		    return {data:defval}
		  }""", "unknow", {"data": "unknow"}),
    )
    def test_mock_and_restore_wx_method(self, method, functionDeclaration, args, result):
        try:
            current_value = (
                self.app.call_wx_method(method, {"key": "test_mock"}).get("result").get("result")
            )
        except minium.MiniAppError as e:
            current_value = e
        if functionDeclaration:
            # 有functionDeclaration情况下 result参数是预期返回
            self.app.mock_wx_method(method, functionDeclaration=functionDeclaration, args=args)
        else:
            self.app.mock_wx_method(method, result=result)
        mock_value = (
            self.app.call_wx_method(method, {"key": "test_mock"}).get("result").get("result")
        )
        self.app.restore_wx_method(method)
        self.assertDictEqual(mock_value, result, "mock success")
        if isinstance(current_value, minium.MiniAppError):
            self.assertRaises(
                minium.MiniAppError, self.app.call_wx_method, method, {"key": "test_mock"}
            )
        else:
            real_value = (
                self.app.call_wx_method(method, {"key": "test_mock"}).get("result").get("result")
            )
            self.assertDictEqual(real_value, current_value, "restore success")
```

---

## restore_wx_method() :id=restore_wx_method
> 去掉函数的mock

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|method|str|Not None|函数名|

**Returns:**
- None

**代码实例见[mock_wx_method](#mock_wx_method)**

---

## hook_wx_method() :id=hook_wx_method
> hook小程序API的调用，能hook的函数参考[小程序的API](https://developers.weixin.qq.com/miniprogram/dev/api/)。


**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|method|str|Not None|函数名|
|before|func|None|在需要 hook 的方法之前调用|
|after|func|None|在需要 hook 的方法之后调用|
|callback|func|None|在需要 hook 的方法回调之后调用|


**Returns:**
- None

**代码实例:**
```python
#!/usr/bin/env python3
import minium
@minium.ddt_class(testNameFormat="%(name)s_%(index)s")
class AppTest(minium.MiniTest):
	    @minium.ddt_case(
        (
            "setStorage",
            {
                "data": c_time,
                "key": "test_storage",
            },
            None,
            {"errMsg": "setStorage:ok"}
        ),
        ("getStorageSync", "test_storage", c_time, None),
        (
            "getStorage", 
            {
                "key": "test_storage",
            }, None,
            {"errMsg": "getStorage:ok", "data": c_time}),
    )
    def test_hook_and_restore_wx_method(self, data):
        [method_name, input_args, expected_return, expected_callback] = data
        before_called = threading.Semaphore(0)
        after_called = threading.Semaphore(0)
        callback_called = threading.Semaphore(0)
        before_args = None
        after_args = None
        callback_args = None

        def before(args):
            nonlocal before_args
            before_called.release()
            before_args = args

        def after(args):
            nonlocal after_args
            after_called.release()
            after_args = args
                
        def callback(args):
            nonlocal callback_args
            callback_called.release()
            callback_args = args

        self.app.hook_wx_method(method_name, before=before, after=after, callback=callback)
        self.app.call_wx_method(method_name, input_args)
        self.assertTrue(before_called.acquire(timeout=10), "before called")
        self.assertTrue(after_called.acquire(timeout=10), "after called")
        if not method_name.endswith("Sync"):
            self.assertTrue(callback_called.acquire(timeout=10), "callback called")
            if isinstance(expected_callback, dict):
                self.assertDictEqual(expected_callback, callback_args[0], "before ok")
            else:
                self.assertEqual(expected_callback, callback_args[0], "before ok")
        if isinstance(input_args, dict):
            self.assertDictEqual(input_args, before_args[0], "before ok")
        else:
            self.assertEqual(input_args, before_args[0], "before ok")
        if isinstance(expected_return, dict):
            self.assertDictEqual(expected_return, after_args[0], "before ok")
        else:
            self.assertEqual(expected_return, after_args[0], "before ok")
        # 释放hook，不应再有回调
        self.app.release_hook_wx_method(method_name)
        self.app.call_wx_method(method_name, input_args)
        self.assertFalse(before_called.acquire(timeout=5), "before 不应再有回调")
        self.assertFalse(after_called.acquire(timeout=5), "after 不应再有回调")
        if not method_name.endswith("Sync"):
            self.assertFalse(callback_called.acquire(timeout=5), "callback 不应再有回调")
```
---

## release_hook_wx_method() :id=release_hook_wx_method
> 释放hook小程序API的调用。


**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|method|str|Not None|函数名|


**Returns:**
- None

**代码实例见[hook_wx_method](#hook_wx_method)**

---

## hook_current_page_method() :id=hook_current_page_method
> hook当前页面上的方法。<br>如 `button` 上 `bindtap="testtap"`, `hook_current_page_method("testtap", callback)`即可在点击按钮时获得回调 


**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|method|str|Not None|方法名|
|callback|func|None|方法被调用前回调函数|


**Returns:**
- None

**代码实例:**

```python
import minium, threading
@minium.ddt_class
class TestApp(minium.MiniTest):
    def test_hook_current_page_method(self):
        self.app.redirect_to("/pages/testapp/testapp")
        excepted_callback_args = {"test": 1}
        callback_called = threading.Semaphore(0)
        excepted_callback = True
        callback_args = None
        def callback(args):
            nonlocal callback_args
            if not excepted_callback:
                raise Exception("不应有call回调")
            callback_called.release()
            print(f"callback {args}")
            callback_args = args

        self.app.hook_current_page_method("testpagehook", callback)
        self.app.current_page.call_method("testpagehook", [excepted_callback_args])
        if excepted_callback:
            self.assertTrue(callback_called.acquire(timeout=10), "callback called")
            if isinstance(excepted_callback_args, dict):
                self.assertDictEqual(
                    excepted_callback_args, callback_args[0] if len(callback_args) > 0 else None, "callback ok"
                )
            else:
                self.assertEqual(excepted_callback_args, callback_args[0] if len(callback_args) > 0 else None, "callback ok")
```

---

## release_hook_current_page_method() :id=release_hook_current_page_method
> 释放当前页面方法的监听函数。


**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|method|str|Not None|函数名|


**Returns:**
- None

---

## mock_request() :id=mock_request
> mock `wx.request` 方法，根据正则匹配结果返回特定构造的数据. 匹配顺序与调用顺序一致（先加入的规则先匹配）

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|rule|str\|dict|Not None|规则，如类型为str，则默认匹配`url`|
|success|dict|None|成功回调结果，与参数`fail`需二选一|
|fail|str\|dict|None|失败回调结果，如类型为str，会自动填充成基础库返回格式，与参数`success`需二选一|

**Returns:**
- None

**代码实例:**
```python
#!/usr/bin/env python3
import minium
class RequestTest(minium.MiniTest):
    def test_mock_request(self):
        self.app.restore_request()  # 清空规则
        mock_resp1 = {"data": "mock result1", "statusCode": 200}
        mock_resp2 = {"data": "mock result2", "statusCode": 200}
        mock_resp3 = {"data": "mock result3", "statusCode": 200}
        rule1 = ".*/SendMsg\\?.*"
        url1 = "http://minitest.weixin.qq.com/SendMsg?content=test"
        rule2 = {"url": ".*/SendMsg$"}
        url2 = "http://minitest.weixin.qq.com/SendMsg"
        rule3 = {"url": ".*/SendMsg.*", "data": {"content": "^\\d+$"}}
        url3 = "http://minitest.weixin.qq.com/SendMsgData"
        data = {"content": "12557"}
        # 加入规则1
        self.app.mock_request(rule1, success=mock_resp1)
        result = self.app.call_wx_method("request", [{"url": url1}]).get("result", {}).get("result")
        self.assertDictEqual(result, mock_resp1)                # 返回mock1的数据
        with self.assertRaises(minium.MiniAppError):
            self.app.call_wx_method("request", [{"url": url2}]) # 规则不匹配，调用原request方法
        # 加入规则2
        self.app.mock_request(rule2, success=mock_resp2)
        result = self.app.call_wx_method("request", [{"url": url2}]).get("result", {}).get("result")
        self.assertDictEqual(result, mock_resp2)                # 返回mock2的数据
        # 加入规则3
        self.app.mock_request(rule3, success=mock_resp3)
        result = self.app.call_wx_method("request", [{"url": url2, "data": data}]).get("result", {}).get("result")
        self.assertDictEqual(result, mock_resp2)                # 虽然与规则3也匹配，但优先匹配规则2，返回mock2的数据
        result = self.app.call_wx_method("request", [{"url": url3, "data": data}]).get("result", {}).get("result")
        self.assertDictEqual(result, mock_resp3)                # 返回mock3的数据
```

---

## restore_request() :id=restore_request
> 清除掉所有mock request的匹配规则

**Parameters:**
- None

**Returns:**
- None

**代码实例见[mock_request](#mock_request)**

---

## get_all_pages_path() :id=get_all_pages_path
> 获取所有已配置的页面路径

**Returns:** 
- `list`

**代码实例:**
```python
import minium
class AppTest(minium.MiniTest):
    def test_get_all_pages_path(self):
        all_pages_path = self.app.get_all_pages_path()
        self.assertListEqual(
            [
                "pages/index/index",
                "pages/mine/mine",
                "pages/login/wechatlogin",
                "pages/login/selflogin",
                "pages/testapp/testapp",
                "pages/testpage/testpage",
                "pages/testelement/testelement",
                "pages/testnative/testnative",
                "pages/exception_page/exception_page"
            ],
            all_pages_path,
            "test ok",
        )
```

---

## get_current_page() :id=get_current_page
> 获取当前顶层页面

**Returns:** 
- [Page](minium/Python/api/Page)

**代码实例:**
```python
import minium
class AppTest(minium.MiniTest):
    def test_get_current_page(self):
        page = self.app.get_current_page()  # 同self.app.current_page
        self.assertIsNotNone(page.path)
        self.assertNotEqual("", page.path)
```

---

## get_page_stack() :id=get_page_stack
> 获取当前小程序页面栈

**Returns:** 
- list[[Page](minium/Python/api/Page)]

**代码实例:**
```python
import minium
class AppTest(minium.MiniTest):
    def test_get_page_stack(self):
        self.app.go_home()
        page1 = self.app.get_current_page()
        self.app.navigate_to("/pages/testapp/testapp")
        pages = self.app.get_page_stack()
        self.assertEqual("/pages/testapp/testapp", pages[1].path)
        self.assertEqual(page1.path, pages[0].path)
```

---

## go_home() :id=go_home
> 跳转到小程序首页

**Returns:** 
- [Page](minium/Python/api/Page)

---

## navigate_to() :id=navigate_to
> 以导航的方式跳转到指定页面

!> 不能跳到 tabbar 页面。支持相对路径和绝对路径, 小程序中页面栈最多十层

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|url|str|Not None|页面路径|
|params|dict|None|页面参数|
|is_wait_url_change|bool|True|是否等待新的页面跳转|

*PS: 页面路径规则：*

- /page/tabBar/API/index: 绝对路径,最前面为/
- tabBar/API/index: 相对路径, 会被拼接在当前页面父节点的路径后面

**路径后可以带参数。参数与路径之间使用 ? 分隔，参数键与参数值用 = 相连，不同参数用 & 分隔；如 'path?key=value&key2=value2'**

**Returns:** 
- [Page](minium/Python/api/Page)

**代码实例:**
```python
import minium
class AppTest(minium.MiniTest):
    def test_navigate_to_and_back(self):
        pass_page = self.app.get_current_page()
        query = {"value1": "1", "value2": "dd"}
        page = self.app.navigate_to("/pages/testapp/testapp", query)
        current_page = self.app.get_current_page()
        self.assertEqual("/pages/testapp/testapp", page.path)
        self.assertDictEqual(query, page.query)
        self.assertDictEqual(current_page.query, page.query)
        self.app.navigate_back()
        page = self.app.get_current_page()
        self.assertEqual(pass_page.path, page.path)
        self.assertDictEqual(pass_page.query, page.query)
```

---

## navigate_back() :id=navigate_back
> 关闭当前页面，返回上一页面或多级页面。

!> 如果超出当前页面栈最大层数，返回首页

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|delta|int|1|返回的层数|


**Returns:** 
- [Page](minium/Python/api/Page)

**代码实例见[navigate_to](#navigate_to)**

---

## redirect_to() :id=redirect_to
> 关闭当前页面，重定向到应用内的某个页面

!> 不允许跳转到 tabbar 页面

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|url|str|Not None|页面路径|
|params|dict|None|页面参数|
|is_wait_url_change|bool|True|是否等待新的页面跳转|

*PS: 页面路径规则：*

- /page/tabBar/API/index: 绝对路径,最前面为/
- tabBar/API/index: 相对路径, 会被拼接在当前页面父节点的路径后面

**路径后可以带参数。参数与路径之间使用 ? 分隔，参数键与参数值用 = 相连，不同参数用 & 分隔；如 'path?key=value&key2=value2'**

**Returns:** 
- [Page](minium/Python/api/Page)

**代码实例:**
```python
import minium
class AppTest(minium.MiniTest):
    def test_redirect_to(self):
        pages = self.app.get_page_stack()
        query = {"value1": "1", "value2": "dd"}
        self.app.redirect_to("/pages/testapp/testapp", query)
        page = self.app.get_current_page()
        self.assertEqual("/pages/testapp/testapp", page.path)
        self.assertDictEqual(query, page.query)
        self.assertEqual(len(pages), len(self.app.get_page_stack()), "len not change")
```

---

## relaunch() :id=relaunch
> 关闭所有页面，打开到应用内的某个页面

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|url|str|Not None|页面路径|

*PS: 页面路径规则：*

- /page/tabBar/API/index: 绝对路径,最前面为/
- tabBar/API/index: 相对路径, 会被拼接在当前页面父节点的路径后面

**路径后可以带参数。参数与路径之间使用 ? 分隔，参数键与参数值用 = 相连，不同参数用 & 分隔；如 'path?key=value&key2=value2'**

**Returns:** 
- [Page](minium/Python/api/Page)

**代码实例:**
```python
import minium
class AppTest(minium.MiniTest):
    def test_relaunch(self):
        self.app.navigate_to("/pages/testpage/testpage")
        query = {"value1": "1", "value2": "dd"}
        self.app.relaunch("/pages/testpage/testpage", query)
        pages = self.app.get_page_stack()
        self.assertEqual(1, len(pages))
        page = pages[0]
        self.assertEqual("/pages/testpage/testpage", page.path)
        self.assertDictEqual(query, page.query)
```

---

## switch_tab() :id=switch_tab
> 跳转到 tabBar 页面

!> 会关闭其他所有非 tabBar 页面

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|url|str|Not None|需要跳转的 tabBar 页面的路径（需在 app.json 的 tabBar 字段定义的页面），路径后不能带参数|


**Returns:** 
- [Page](minium/Python/api/Page)

**代码实例:**
```python
import minium
class AppTest(minium.MiniTest):
    def test_switch_tab(self):
        self.app.switch_tab("/pages/mine/mine")
        page = self.app.current_page
        self.assertEqual("/pages/mine/mine", page.path)
```

---

## get_perf_time() :id=get_perf_time
> 查询小程序的性能指标，跟stop_get_perf_time配对使用，2.11.0开始支持

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|entry_types|list|Not None|可选项为['render', 'script', 'navigation', 'loadPackage']中的1个或多个|

**Returns:**
- None

**代码实例见[stop_get_perf_time](#stop_get_perf_time)**

---

## stop_get_perf_time() :id=stop_get_perf_time
> 结束查询，跟get_perf_time配对使用，2.11.0开始支持


**Parameters:**
- None

**Returns:**
- time list,duration单位为ms

**代码实例:**
```python
#!/usr/bin/env python3
import minium
class AppTest(minium.MiniTest):
	self.app.get_perf_time(entry_types=["navigation"])
        self.app.navigate_to("/pages/testapp/testapp")
        self.app.redirect_to("/pages/testpage/testpage")
        perf_data = self.app.stop_get_perf_time()
        print(perf_data)
        self.assertListEqual(
            [
                {
                    "name": data["name"],
                    "entryType": data["entryType"],
                    "navigationType": data["navigationType"],
                    "path": data["path"],
                }
                for data in perf_data
            ],
            [
                {
                    "name": "route",
                    "entryType": "navigation",
                    "navigationType": "navigateTo",
                    "path": "pages/testapp/testapp",
                },
                {
                    "name": "route",
                    "entryType": "navigation",
                    "navigationType": "redirectTo",
                    "path": "pages/testpage/testpage",
                },
            ],
        )
        for data in perf_data:
            self.assertIsNot(0, data["startTime"])
            self.assertIsNot(0, data["duration"])
            self.assertIsNot(0, data["navigationStart"])
```

---