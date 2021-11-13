<!--
 * @Author: yopofeng
 * @Date: 2021-02-25 16:56:11
 * @LastEditTime: 2021-09-10 22:44:21
 * @LastEditors: yopofeng
 * @Description: 
-->
# `Class` minium.MiniTest :id=minium-MiniTest
> `MiniTest`是minium中继承自`unittest.TestCase`的测试基类, 你可以在testcase中使用框架实例化好的Minium/App/Native实例。
> 也可以使用`unittest`中的各种断言函数

**Properties:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|mini|[minium.Minium](minium/Python/api/Minium)|None|Minium实例，可直接调用minium.Minium中的方法|
|app|[minium.App](minium/Python/api/App)|None|App实例，可直接调用minium.App中的方法|
|page|[minium.Page](minium/Python/api/Page)|None|当前页面Page实例，可直接调用minium.Page中的方法|
|native|[minium.Native](minium/Python/api/Native)|None|Native实例，可直接调用minium.Native中的方法|

**代码示例:** 

```python
#!/usr/bin/env python3
import minium
class FirstTest(minium.MiniTest):
    def test_get_system_info(self):
        sys_info = self.mini.get_system_info()
        self.assertIn("SDKVersion", sys_info)

if __name__ == "__main__":
    import unittest
    loaded_suite = unittest.TestLoader().loadTestsFromTestCase(FirstTest)
    result = unittest.TextTestRunner().run(loaded_suite)
    print(result)
```

**运行**

直接执行py文件或参考[例子](minium/Python/framework/example)使用命令行启动