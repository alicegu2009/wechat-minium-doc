# `Class` minium.Minium :id=minium-Minium

> `Minium`负责初始化整个自动化框架, 提供了`Driver`的启动接口, 以及测试结束之后回收资源能力

可以通过实例化[`minium.Minium`](#init)或从[`minium.MiniTest.mini`](minium/Python/framework/Minitest)获得`Minium`实例

**Properties:**

|名称| 类型| 默认值| 说明|
| :----- | ----- | :-----: | :----- |
|conf|[minium.MiniConfig](minium/Python/framework/config.md#基础项目配置)|None|minium的配置项|
|app|[minium.App](minium/Python/api/App)|None|App实例，可直接调用minium.App中的方法|
|version|str|None|minium版本号|

---

## Minium() :id=init
> 初始化函数

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|conf|dict|None|配置，参考 [配置文件](minium/Python/framework/config.md)|
|uri|str|ws://localhost|开发者工具自动化 WebSocket 地址|

**代码示例:** 

```python
import minium
mini = minium.Minium({
    "project_path": "path/to/miniprogram/project",  # 小程序项目目录地址
    "dev_tool_path": "path/to/cli"                  # 开发者工具cli地址，如果没有修改过默认安装路径可不填此项
})
print(mini.get_system_info())
```

---

## clear_auth() :id=clear_auth
> 清除用户授权信息. 

!> 公共库 2.9.4 开始生效

**Returns:** 
- `None`
---

## get_system_info() :id=get_system_info
> 获取系统信息, 同 [wx.getSystemInfo](https://developers.weixin.qq.com/miniprogram/dev/api/base/system/system-info/wx.getSystemInfo.html)

**Returns:** 
- 系统信息, `dict`

---

## enable_remote_debug() :id=enable_remote_debug
> 在真机上运行自动化。如果配置了`platform`为`ios`或`android`，则实例化时会自动调用。

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|use_push|bool|True| 是否直接推送到客户端 |
|path|str|None| 远程调试二维码的保存路径 |
|connect_timeout|int|180| 连接超时时间，单位 s |

**Returns:** 
-  `None`

---

## reset_remote_debug() :id=reset_remote_debug
> 重置远程调试，解决真机调试二维码扫码界面报-50003 等常见错误。一般供测试框架调用

**Returns:** 

- `None`
---

## shutdown() :id=shutdown
> 测试结束时调用, 停止 微信开发者IDE 以及 minium, 并回收资源。一般供测试框架调用





