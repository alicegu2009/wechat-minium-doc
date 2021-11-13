# 元素定位 :id=selector

minium 通过 WXSS 选择器来定位元素的，目前小程序仅支持以下的选择器：

| 选择器 | 样例 | 样例描述 |
| :---: | :---: | :---: |
|.class	|.intro	|选择所有拥有 class="intro" 的组件|
|#id	|#firstname|	选择拥有 id="firstname" 的组件|
|element|	view|	选择所有 view 组件|
|element, element|	view, checkbox|	选择所有文档的 view 组件和所有的 checkbox 组件|
|::after|	view::after|	在 view 组件后边插入内容|
|::before|	view::before|	在 view 组件前边插入内容|
|a>>>b|	custom-element1>>>.custom-element2>>>.the-descendant|	跨自定义组件的后代选择器|
|/xpath|	/view[1]|	第一个view元素|

*PS:跨自定义组件的后代选择器中 `custom-element1` 和 `.custom-element2` 必须是自定义组件标签或者能获取到自定义组件的选择器*

## 简单选择器

一个元素的选择器通常是以下格式组成的：

!> id 不能以数字开头，命名规范问题

> tageName + #id + .className

这其实是三个选择器组合的结果，其中：

- `tagName` ：类型选择器，标签名称，view、checkbox 等等，选择所有指定类型的最简单方式。
- `id`：ID 选择器，自定义给元素的唯一 ID，使用时前面跟着 `#` 号，这是选择单个元素的最有效的方式。
- `className`：类选择器，由一个点`.`以及类后面的类名组成，存在多个类的时候可以以点为间隔一直拼接下去。

e.g：
```html
<view id="main" class="page-section page-section-gap" style="text-align: center;"></view>
```
假如要查找像上面这一个元素的话，他的选择器会像是下面这样：

```JavaScript
view#main.page-section.page-section-gap
```

另一种写法

```javaScript
view[id='main'][class='page-section page-section-gap']
```

所以，通常我们只需要关注一个元素的 id 和 class 即可。但是有时候有的情况是，有一堆没有 id ，相同 class 的同名标签，我们没办法通过 id 或者 class 来区分了，比如我们 demo 的 input 页面：

```html
<view class="page-body">
    <view class="page-section">
      <view class="weui-cells__title">可以自动聚焦的input</view>
      <view class="weui-cells weui-cells_after-title">
        <view class="weui-cell weui-cell_input">
          <input class="weui-input" auto-focus placeholder="将会获取焦点" bindinput="bindKeyInput"/>
        ...
          <input class="weui-input" maxlength="10" placeholder="最大输入长度为10" />
        ...
          <input class="weui-input"  maxlength="10" bindinput="bindKeyInput" placeholder="输入同步到view中"/>
        ...
          <input class="weui-input"  bindinput="bindReplaceInput" placeholder="连续的两个1会变成2" />
        ...
          <input class="weui-input"  bindinput="bindHideKeyboard" placeholder="输入123自动收起键盘" />
        ...
          <input class="weui-input" type="number" placeholder="这是一个数字输入框" />
        ...
          <input class="weui-input" password type="text" placeholder="这是一个密码输入框" />
        ...
          <input class="weui-input" type="digit" placeholder="带小数点的数字键盘"/>
        ...
          <input class="weui-input" type="idcard" placeholder="身份证输入键盘" />
        ...
          <input class="weui-input" placeholder-style="color:#F76260" placeholder="占位符字体是红色的" />
        ...
```

我们仔细观察，虽然所有的 input 都有着同样的 class，但其实他们并非都是一模一样的，在实际测试中，我们发现绝大部分的属性都可以用来寻找。

e.g：
```html
<input class="weui-input" type="digit" placeholder="带小数点的数字键盘"/>
```
如上，我们可以通过 `type` 和 `placeholder` 两个属性去找到这个元素。

```javaScript
input[type='digit']
```

或者

```javaScript
input[placeholder='带小数点的数字键盘']
```

亦或者，两者加起来

```javaScript
input[type='digit'][placeholder='带小数点的数字键盘']
```

所以，具体情况具体分析，在我测试的过程中发现，`bindinput` 这一类绑定方法是不能用作寻路依据的。



