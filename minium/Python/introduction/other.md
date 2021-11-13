!> 如果你想在手机上面运行上面的脚本，可以点击开发者工具的`远程调试`,用手机扫码连接，成功打开远程调试之后，再次运行脚本，脚本就会在手机上面运行了

关于编写用例的更多内容，请参考[测试框架](minium/Python/framework/framework.md)


## 测试用例执行

### 利用PyCharm CE执行

[PyCharm CE](https://www.jetbrains.com/pycharm/download)是一个免费的IDE，能自动识别python的unittest用例，并且有良好的代码提示功能
比如针对上面的`first_test.py`，鼠标移动到用例的函数直接点击右键:

![pycharm](D:/dddd/weixin/miniumtest/minium-doc/minium-doc/minium/resources/pycharm.png)


### 利用命令行运行用例

在安装包之后，比如我们要运行上面的例子，只需要在命令行输入:

执行结束后，测试数据输出到outputs目录，里面包含一个简易的测试报告，输入` python3 -m http.server 8080 -d outputs`后在浏览器中输入`http://localhost:8080/`可以查看测试报告
其中，更多命令行参数可以通过`miniruntest -h`查看。

?> 我们建议编写用例和调试的时候，可以在IDE上调试和执行。部署测试的时候用命令行，测试输出的结果可以用于存档，如Jenkins的存档或者上传到静态服务器上


### 手机上运行

上面的步骤默认在IDE上运行，如果我们想在手机上运行，比如Android，只需要在用例目录下面新增一个叫`config.json`的配置文件，内容如下：

``` json
 Android：
{
    "platform": "Android"
}
```

然后连接上一台手机，并且保证`adb devices`能够识别手机，有问题请参考[adb文档](https://developer.android.google.cn/studio/command-line/adb#devicestatus)。

然后再执行`miniruntest -m first_test -g`即可。

更多关于手机测试的内容，请参考[测试手机上的小程序](minium/Python/framework/mobile.md)。

!> 注意手机微信上登陆的帐号与开发者工具登陆的帐号需要一致