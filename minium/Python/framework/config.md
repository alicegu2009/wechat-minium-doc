# 项目配置
为了保证同一套代码在IDE，Android，IOS上运行，环境组成比较复杂，所以测试用例的运行依赖于配置文件。

## 基础项目配置
|配置项| 类型| 默认值| 说明|
| :----- | :----- | :----- | :----- |
|platform|str|"ide"| 小程序运行的平台，可选值为：`ide, Android, IOS`|
|project_path|str|""| 小程序代码的项目路径，如果配置了之后，那么需要同时配置dev_tool_path|
|dev_tool_path|str|见[dev_tool_path默认值](#dev_tool_path默认值)|小程序[IDE cli](https://developers.weixin.qq.com/miniprogram/dev/devtools/cli.html)的路径|
|debug_mode|str|"info"|日志打印级别，可选:` error, warn, info, debug`|
|test_port|int|9420| IDE监听的端口，默认为`9420`|
|device_desire|dict|None| 连接手机的参数，跟手机平台有关系|
|account_info|dict|None|账号信息，多账号运行使用，详情见[account_info配置项](#account_info配置项)|
|use_push|bool|True| 是否自动推送远程调试, platform != "ide"时生效 |
|remote_connect_timeout|int|180|远程调试连接超时时间 s|
|request_timeout|int|30|请求连接超时时间 s|
|auto_relaunch|bool|True|每个case启动的时候relaunch到启动页面|
|mock_native_modal|dict|None|仅在ide上生效, mock会引起授权弹窗的方法, 使有弹窗的情况下仍能在ide上运行，具体用法请参考[IDE的mock_native_modal配置项](#IDE的mock_native_modal配置项)|
|mock_request|list|[]|mock网络请求, 具体用法参考[mock_request配置项](#mock_request配置项)|
|auto_authorize|bool|False|自动处理授权弹窗，包括: 获取用户信息，获取位置，获取微信运动数据，获取录音权限，获取相册权限，获取摄像头权限。该配置会hook部分接口，使用`release_hook_wx_method`方法释放`authorize`, `getLocation`, `chooseLocation`, `getWeRunData`, `getUserProfile`时可能导致功能失效|
|audits|bool|False|自动体验测评，生成报告的时机是关闭ide之前，或手动调用Minium实例的`stop_audits`方法|
|outputs|str|outputs|用例运行的结果存放目录|


## 使用minitest测试框架支持的项目配置
> 使用`minitest`命令启动的任务才会加载以下配置

|配置项| 类型| 默认值| 说明|
| :----- | :----- | :----- | :----- |
|enable_app_log|bool|True| 是否监听小程序代码返回的日志|
|enable_network_panel|bool|False| 是否监听所有wx.request的请求和返回，日志生成到request.log|
|close_ide|bool|False| 执行完一个 class 是否关闭 ide，ide 下运行不生效|
|full_reset|bool|False| 执行完一个 class 是否关闭 ide 和 native driver|
|assert_capture|bool|True| 在assert的时候是否截图|

## account_info配置项
> account_info的信息可通过`minitest -a` 获取：

|配置项| 类型| 默认值| 说明|
| :----- | :----- | :----- | :----- |
|wx_nick_name|str|None|  微信账号名称 |
|open_id|str|None| 微信账号的 open_id |

## dev_tool_path默认值
macOS: `/Applications/wechatwebdevtools.app/Contents/MacOS/cli`<br>windows: `C:/Program Files (x86)/Tencent/微信web开发者工具/cli.bat`


## device_desire配置项
> `device_desire`跟平台有关系，不同平台配置不一样。

### Android的device_desire配置项

|配置项| 类型| 默认值| 说明|
| :----- | :----- | :----- | :----- |
|serial|str|null| Android设备号, `adb devices`查看|
|uiautomator_version|int|1| 底层使用的Ui Automator版本， 可选: `1`, `2`|

**ps: device_desire={}默认使用uiautomator2和选择第一个设备**


### IOS的device_desire配置项

#### 使用`tidevice`的`device_desire`配置项

首先检查以下几项
- 运行`tidevice -v`确认`tidevice`安装成功
- 确认`WebDriverAgentRunner`已经成功安装到手机上
- 运行`tidevice xctest --bundle_id "com.*.xctrunner"`有`WebDriverAgent start successfully`的log

|配置项| 类型| 默认值| 说明|
| :----- | :----- | :----- | :----- |
|wda_bundle|str|"com.*.xctrunner"|`WebDriverAgentRunner`的bundle id|
|device_info|dict|| 真机信息|

其中device_info：

|配置项| 类型| 默认值| 说明|
| :----- | :----- | :----- | :----- |
|udid|str|None| 手机的 uuid, 不填则默认使用第一台设备 |

例子:
```json
{
    "wda_bundle": "com.*.xctrunner",
    "device_info": {
        "udid": "00008030-000XXXXXXXXXXXXXX"
    }
}
```

#### 使用wda工程的`device_desire`配置项

|配置项| 类型| 默认值| 说明|
| :----- | :----- | :----- | :----- |
|wda_project_path|str|not null| 自定义 wda 的路径|
|device_info|dict|not null| 真机信息|

其中device_info：

|配置项| 类型| 默认值| 说明|
| :----- | :----- | :----- | :----- |
|udid|str|not null| 手机的 uuid |
|model|str|null| 机型|
|version|str|null| 系统版本|
|name|str|null| 设备名称|


例子:
```json
{
    "wda_project_path": "/Users/sherlock/.npm-global/lib/node_modules/appium/node_modules/appium-webdriveragent",
    "device_info": {
        "udid": "aee531018e668ff1aadee0889f5ebe21axxxxxe8",
        "model": "iPhone XR",
        "version": "12.2.5",
        "name": "sherlock's iPhone"
    }
}
```

## IDE的mock_native_modal配置项

mock_native_modal：

|配置项| 类型| 默认值| 说明|
| :----- | :----- | :----- | :----- |
|weRunData|Dict|None| 参考`wx.getWeRunData`接口返回的结果信息|
|userInfo|Dict|None| 参考`wx.getUserInfo`接口返回的结果信息|
|location|Dict|None| 参考`wx.getLocation`和`wx.chooseLocation`接口返回的结果信息集合|
|locations|Dict|None| 调用`wx.chooseLocation`后，可供`native.map_select_location`选择的地址集合|

```json
{
    "mock_native_modal": {
        "weRunData": {
            "encryptedData": "123",
            "iv": "456=="
        },
        "userInfo": {
            "nickName":"test test",
            "gender":1,
            "language":"zh_CN",
            "city":"",
            "province":"",
            "country":"St.Kitts and Nevis",
            "avatarUrl":"https://xxxx.qq.com"
        },
        "location": {
            "address": "广东省广州市",
            "latitude": 23.0999,
            "longitude": 113.3249,
            "name": "腾讯微信总部"
        },
        "locations": {
            "T.I.T创意园": {
                "address": "广东省广州市海珠区新港中路397号",
                "latitude": 23.10,
                "longitude": 113.33,
                "name": "T.I.T创意园"
            }
        },
    }
}
```

**代码实例:**
```python
#!/usr/bin/env python3
import minium
class TestMockNativeModal(minium.MiniTest):
	def test_chooseLocation(self):
        self.app.navigateTo("/packageAPI/pages/choose-location/choose-location")
        self.app.get_current_page().get_element("button", inner_text="选择位置").tap()
        time.sleep(1)
        self.native.test_allow_get_location(True)  # 授权获取位置
        result = self.native.map_select_location("腾讯微信总部")  # 确认选择位置, 可以选择"腾讯微信总部"和"T.I.T创意园"
        expected_res = {"errMsg":"chooseLocation:ok","name":"腾讯微信总部","address":"广东省广州市",
                        "latitude":23.12463,"longitude":113.36199}
        location = self.test_config.mock_native_modal.get("location", {})
        for key in location:
            if key in expected_res:
                expected_res[key] = location[key]
        self.assertDictEqual(expected_res, result)
```

## mock_request配置项

mock_request中每一项配置：

|配置项| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|rule|str\|dict|Not None|规则，如类型为str，则默认匹配`url`|
|success|dict|None|成功回调结果，与参数`fail`需二选一|
|fail|str\|dict|None|失败回调结果，如类型为str，会自动填充成基础库返回格式，与参数`success`需二选一|

> 以下例子运行效果同[App.mock_request](/minium/Python/api/App#mock_request)中的例子

```json
{
    "mock_request": [
        {
            "rule": ".*/SendMsg\\?.*", 
            "success": {"data": "mock result1", "statusCode": 200}
        },
        {
            "rule": {"url": ".*/SendMsg$"}, 
            "success": {"data": "mock result2", "statusCode": 200}
        },
        {
            "rule": {"url": ".*/SendMsg.*", "data": {"content": "^\\d+$"}}, 
            "success": {"data": "mock result3", "statusCode": 200}
        },
        {
            "rule": {"url": ".*/SendMsg.*"}, 
            "fail": {"errMsg": "request:fail mock fail"}
        },
    ]
}
```

## 例子

```json
{
    "debug_mode": "debug",
    "enable_app_log": true,
    "project_path": "/Users/sherlock/github/miniprogram-demo",
    "dev_tool_path": "/Applications/wechatwebdevtools.app/Contents/MacOS/cli",
    "platform": "ios",
    "close_ide": true,
    "test_port": 9420,
    "assert_capture": true,
    "use_push": true,
    "auto_relaunch": true,
    "remote_connect_timeout": 180,
    "device_desire": {
        "wda_project_path": "/Users/sherlock/.npm-global/lib/node_modules/appium/node_modules/appium-webdriveragent",
        "os_type": "ios",
        "device_info": {
            "udid": "aee531018e668ff1aadee0889f5ebe21axxxxxe8",
            "model": "iPhone XR",
            "version": "12.2.5",
            "name": "sherlock's iPhone"
        }
    }
}
```






