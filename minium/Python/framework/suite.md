# 测试计划
> 测试项目中一般包含大量的测试case，在不同的测试阶段可能需要选取不同的case运行，因此项目中需要配置不同的测试计划

以下是一个使用测试计划进行配置的例子

## 目录结构 {docsify-ignore} 

```
.
├── test
│   └── __init__.py
│   └── first_test.py
│   └── second_test.py
└── config.json
└── suite.json
```

`case`和`配置`编写可参考[例子](minium/Python/framework/example)

## 编写测试计划 {docsify-ignore}
编辑suite文件`suite.json`

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

## 运行测试计划 {docsify-ignore}
```
minitest -s suite.json -c config.json -g
```

更多命令行参数请参考[命令行工具](minium/Python/framework/commandline)
