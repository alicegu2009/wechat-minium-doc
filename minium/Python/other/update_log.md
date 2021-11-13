# 更新日志

## v1.2.1(2021-09-26)
1. `F` fix`Page.wait_data_contains`使用不清晰问题
2. `F` hook Sync方法时，不应注入callback回调
3. `A` 新增自动同意授权配置项`auto_authorize`
4. `F` 修复分享小程序相关接口
5. `A` 新增配置项`audits`，支持自动启动体验评分，并在关闭ide的时候自动在`outputs`目录(配置中的测试结果输出目录)下生成报告

## v1.2.0(2021-09-10)
1. `A` 新增了测试小程序和测试用例工程[git地址](https://git.weixin.qq.com/minitest/minitest-demo.git)
2. `F` 修复多媒体元素(`VideoElement`/`AudioElement`/`LivePlayerElement`/`LivePusherElement`)接口失效问题，并补充了用例
3. `A` 使用新版开发者工具，支持自动打开服务端口
4. `F` 修复`enable_log`接口重复调用会一行log回调多次的问题
5. `A` `App.screen_shot`在配置了真机信息的情况下，也支持真机截图了
6. `A` `App.call_wx_method`和`App.mock_wx_method`支持调用和mock插件上的wx方法(2.19.3基础库支持)
7. `F` App重复实例化重复注入监听函数的问题
8. `F` fix断言函数`msg`参数中包含`/`等字符时，自动截图报错问题
9. `A` `Page.element_is_exists`支持更多条件
10. `A` 新增`App.hook_current_page_method`和`App.release_hook_current_page_method`方法，支持hook当前页面的函数调用
11. `F` 尝试修复远程调试拉起失效问题

## v1.1.0(2021-08-05)
1. `A` `get_element`支持`>>>`跨自定义组件选择器，[详情](/minium/Python/api/Page#get_element)
2. `F` `allow_authorize`接口在ios端传参问题
3. `A` [Page.get_element](/minium/Python/api/Page#get_element)和[Element.get_element](/minium/Python/api/Element#get_element) 在获取不到元素的时候，会抛`MiniElementNotFoundError`

## v1.0.9(2021-07-19)
1. `A` 新增多种接口处理各种授权弹窗，以及一个通用接口[`allow_authorize`](/minium/Python/api/Native#allow_authorize)处理非特定的授权弹窗
2. `A` 新增[`allow_send_subscribe_message`](/minium/Python/api/Native#allow_send_subscribe_message)处理订阅消息授权弹窗

## v1.0.8(2021-06-22)
1. `A` `minium.ddt_*`支持自定义test method命名规则，详情见[数据驱动测试](/minium/Python/introduction/sample#test_ddt)
2. `A` 增加 [`app.mock_request`](/minium/Python/api/App#mock_request) 和 [`app.restore_request`](/minium/Python/api/App#restore_request)方法，用于细化mock网络请求返回
3. `A` 配置项新增[`mock_request`](/minium/Python/framework/config#mock_request配置项)项，用以配置整个测试任务运行时，需要mock的网络请求

[下载](https://minitest.weixin.qq.com/minium/Python/dist/minium-1.0.8.zip)

## v1.0.7(2021-05-24)
1. `F` 优化log显示
2. `U` 优化启动逻辑
3. `U` 细化报错类型

[下载](https://minitest.weixin.qq.com/minium/Python/dist/minium-1.0.7.zip)

## v1.0.6(2021-4-12)
1. `F` fix assert 失败后，报告仍然显示成功的问题
2. `A` `assert_capture`配置项同时控制setup和teardown的截图
3. `F` fix部分case没有正确生成报告的问题

## v1.0.5(2021-03-25)
1. `F` setupclass失败后，报告丢失问题
1. `F` 部分case的报告没有正常生成的问题
2. `A` 配置新增`mock_native_modal`，支持`ide`环境下mock可能出现弹窗的方法，详情见[IDE的mock_native_modal配置项](/minium/Python/framework/config#IDE的mock_native_modal配置项)

## v1.0.4(2021-03-02)
1. `F` pycharm中使用单测能力丢失的问题
2. `F` 使用unittest启动不能识别case的问题

## v1.0.3(2021-02-25)

1. `F` fix安卓handle_modal失效的问题

## v1.0.2(2021-02-01)

1. `A` 增加`enable_network_panel`配置项，监听所有`wx.request`接口的request和response
2. `F` fix crash后无法继续后面的用例的问题

## v1.0.0b-beta2(2021-01-12)

1. `A` page增加wait_for接口
2. `A` android增加click_point点击坐标接口
3. `u`  fix wait_data_contains bug

## v1.0.0b-beta2(2021-01-12)

1. `U` 截图接口修改capture的参数 

## v1.0.0b-beta2(2020-10-28)

1. `A` 新增接口 text_exists ， text_click 

## v1.0.0b-beta2(2020-10-27)

1. `U` bug fix
2. `A` 新增接口get_pay_value,close_payment_dialog,input_pay_password

## v1.0.0b-beta2(2020-09-03)

1. `U` bug fix
2. `A` 新增 release_hook_wx_method 接口

## v1.0.0-beta(2020-05-01)

1. `A` 新增 reset_remote_debug 接口
2. `U` 更新 handle_modal 接口处理，取消断言改为 warnning 提示，并返回成功状态
3.  `U` 在 case setup 的时候主动检测是否出现远程调试断开的情况
4.  `U` 针对 get_element()超时逻辑做调整，默认超时时间调整为 0，即不重试，有等待需要的可自行调整重试等待时间。另外，对于超时的情况不再 raise Exception，改成 warnning 的形式，并返回空的 element list，使用者可通过返回结果进行判断
5.  `A` 为 scroll-view element 添加四个可获取属性，scrollTop，scrollLeft，scrollWidth，scrollHeight，可用于坐标系运算
6.  `U` 支持 cli v2
7.  `A` 新增 request_timeout 参数，与 remote_connect_timeout 区分开，用作 send 请求等待的超时时间
8.  `D` 移除 CaseService, 框架内不再提供上层的用例调度管理模块
9.  `U` 优化运行体验，针对连接失败，启动失败，配置错误作出相应的重试和提示
10. `F` log 等级设置无效
11. `A` 新增 ide 截图接口（仅能截取 webView 部分）
12. `F` bug fix
13. `A` 新增 Android 性能数据获取接口
14. `F` 全平台默认多线程使用 spawn 模式

## v0.0.3(2019-10-28)

1. `U` bug fix
2. `A` 新增监听小程序 JS 错误
3. `U` 异步请求结果保存
4. `U` 更新部分 native 操作路径，以适应 7.0.7 版本微信
5. `U` 返回 Page.call_method 执行的结果
6. `A` 新增小程序事件监听机制
7. `A` 支持数据驱动
8. `A` 新增 @exit_when_err 装饰器，用于装饰某条用例，当这条用例失败时便不继续往下执行
9. `U` 适配 iOS 13
10. `A` Android 支持部分性能数据收集
11.  `A` minitest 命令新增 `--source` 参数，用于添加文件查找路径
12.  `U` iOS 取消保存 iproxy log
13.  `U` evaluate() 增加参数 `sync` 指定是否同步执行，默认异步执行
14.  `U` 测试报告大改版
15.  `A` Element 支持 touchstart、touchmove、touchend 事件触发
16.  `A` Element 新增接口 move() ，支持移动延时以及平滑移动
17.  `A` Page 新增接口 element_is_exists() ，查询元素是否存在
18.  `A` 支持自定义组件 get/set data 以及 call_method()

## v0.0.2(2019-08-20)

1. `U` bug fix
2. `A` 新增 AppService 代码注入
3. `A` 新增将函数暴露给小程序调用的能力
4. `A` 新增配置文件 yaml 支持
5. `A` 新增支持 VideoContext、AudioContext、LivePlayerContext、LivePusherContext 控制
6. `A` 新增 Android 配置可选 uiautomator 版本
7. `A` 新增显示 uiautomator apk 路径的命令
8. `A` 新增命令行打印构建参数
9. `A` 新增配置控制assert截图

## v0.0.1(2019-06-26)

1. `A` 新增小程序层控制
2. `A` 新增 Native 层控制
3. `A` 新增真机运行
4. `A` 新增自动化测试集成框架
5. `A` 新增测试报告生成
6. `A` 新增命令行调用命令：`miniruntest` `miniruntest` `mininative`


