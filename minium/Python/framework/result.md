# 测试结果 {docsify-ignore} 
每条用例的测试结果我们会存放到一个目录里面，里面包含:

1. 包含用例执行信息的json文件
2. 用例运行中的截图
3. 用例运行中的日志
4. 小程序运行中的日志

基于这些数据可以生成测试报告，也可以做一些存档的事情。

# 测试报告 {docsify-ignore} 
根据用例的执行结果，我们基于[Vue](https://cn.vuejs.org/v2/guide/installation.html)和[element](https://element.eleme.cn/#/zh-CN)提供一个简洁的测试报告：[例子](http://localhost:3000/minium/Python/minium_report/index.html)。


报告生成有2种方式:

1. 执行用例的时候加上`-g`参数
1. 针对已经生成的用例结果目录
   ```shell
   minireport input_path output_path
   ```
   `output_path`里面会生成有报告的入口。


生成报告之后，在对应的目录下面有index.html文件，但是我们不能直接用浏览器打开这个
文件，需要把这个目录放到一个静态服务器上。以下方式都是可行的:

1. 本地执行`python3 -m http.server 12345 -d /path/to/dir/of/report `，然后浏览器输入:`http://localhost:12345/`
   
  PS: *其中`/path/to/dir/of/report`为上文的`output_path`*
2. 利用nginx的配置:
    ```nginx
    server {
      listen 80;
      server_name  your.domain.com;

      location / {
        alias /path/to/dir/of/report;
        index index.html;
      }
    }
    ```



