# 命令行工具

测试用例的执行可以用执行unittest的方式，也可以用我们提供的脚本`minitest`来加载用例，相关的参数说明如下:

## minitest 命令 {docsify-ignore} 

- **-h, --help**: 使用帮助。

- **-v, --version**:  查看 minium 的版本。

- **-p PATH/--path PATH**: 用例所在的文件夹，默认当前路径。

- **-m MODULE_PATH, --module MODULE_PATH**: 用例的包名或者文件名

- **--case CASE_NAME**: `test_`开头的用例名

- **-s SUITE, --suite SUITE**:[测试计划](minium/Python/framework/suite)文件，文件的格式如下:

  ```json
  {
    "pkg_list": [
      {
        "case_list": [
          "test_*"
        ],
        "pkg": "test.*_test"
      }
    ]
  }
  ```
  
  suite.json的`pkg_list`字段说明要执行用例的内容和顺序，`pkg_list`是一个数组，每个数组元素是一个匹配规则，会根据`pkg`去匹配包名，找到测试类，然后再根据`case_list`里面的规则去查找测试类的测试用例。可以根据需要编写匹配的粒度。注意匹配规则不是正则表达式，而是[通配符](https://www.gnu.org/software/findutils/manual/html_node/find_html/Shell-Pattern-Matching.html)。

  ?> 测试文件可以指定特定的用例，规定执行顺序

- **-c CONFIG, --config CONFIG**:配置文件名，配置项目参考[配置文件](minium/Python/framework/config.md)
- **-g, --generate**: 生成网页测试报告
- **--module_search_path [SYS_PATH_LIST [SYS_PATH_LIST ...]]**: 添加 module 的搜索路径
- **-a, --accounts**:  查看开发者工具当前登录的多账号, 需要通过 9420 端口,以自动化模式打开开发者工具
- **--mode RUN_MODE**:  选择以`parallel`(并行)或者`fork`(复刻)的方式运行用例
