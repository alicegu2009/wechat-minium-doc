<!--
 * @Author: yopofeng
 * @Date: 2021-02-25 16:56:11
 * @LastEditTime: 2021-08-25 14:32:43
 * @LastEditors: yopofeng
 * @Description: 
-->
# <font face="Adobe Garamond Pro" color=#008B8B>framework</font> {docsify-ignore} 

minium提供一个基于[unittest](https://docs.python.org/3/library/unittest.html)封装好的测试框架，利用这个简单的框架对小程序测试可以起到事半功倍的效果。

测试基类[Minitest](minium/Python/framework/Minitest.md)会根据[测试配置](minium/Python/framework/config.md)进行测试，minitest向上继承了`unittest.TestCase`，并做了以下改动：

1. 加载读取测试配置
1. 在合适的时机初始化[minium.Minium](minium/Python/api/Minium)、[minium.App](minium/Python/api/App)和[minium.Native](minium/Python/api/Native)
1. 根据配置打开IDE，拉起小程序项目和或自动打开真机调试
1. 注入测试逻辑
1. 拦截assert调用，记录检验结果
1. 记录运行时数据和截图，用于测试报告生成

使用MiniTest可以大大降低小程序测试成本。如何快速使用测试框架进行测试，可参考[例子](minium/Python/framework/example)


