- [一、启动浏览器](#一启动浏览器)
  -  [1. 同步方式（sync_api）](#1-同步方式sync_api)
  - [2. 异步方式（sync_api）](#2-异步方式sync_api)
- [二、playwright 基础操作](#二playwright-基础操作)
  - [1. 窗口最大化](#1-窗口最大化)
  - [2. 设置窗口的指定大小](#2-设置窗口的指定大小)
  - [3. 启动本地浏览器](#3-启动本地浏览器)
  - [4. 在打开的浏览器上继续操作](#4-在打开的浏览器上继续操作)
- [三、元素定位](#三元素定位)
  - [1. CSS 或 XPath 选择器](#1-css-或-xpath-选择器)
  - [2. 文本选择器](#2-文本选择器)
  - [3. 组合定位](#3-组合定位)
  - [4. playwright内置定位器](#4-playwright内置定位器)
    - [4.1 page.get_by_text()](#41-pageget_by_text)
    - [4.2 page.get_by_role()](#42-pageget_by_role)
    - [4.3 page.get_by_alt_text()](#43-pageget_by_alt_text)
  - [5. ElementHandle元素句柄](#5-elementhandle元素句柄)
- [四、元素操作](#四元素操作)
  - [1. 输入文字](#1-输入文字)
    - [1.1 fill()](#11-fill)
    - [1.2 type()](#12-type)
  - [2. 点击](#2-点击)
  - [3. 悬停](#3-悬停)
  - [4. iframe](#4-iframe)
    - [4.1 iframe定位](#41-iframe定位)
    - [4.2 案例](#42-案例)
    - [4.3 获取页面上所有的面上所有的iframe](#43-获取页面上所有的面上所有的iframe)
  - [5. 截图](#5-截图)
    - [5.1 screenshot 截图](#51-screenshot-截图)
    - [5.2 截长图](#52-截长图)
    - [5.3 截取单个元素](#53-截取单个元素)

## 一、启动浏览器

### 1. 同步方式（sync_api）

```python
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    # 启动浏览器
    browser = p.chromium.launch(headless=False)
    # 创建标签页
    page = browser.new_page()
    # 打开指定网站
    page.goto("www.baidu.com")
    # 关闭浏览器
    browser.close()

```

### 2. 异步方式（sync_api）

```python
import asyncio
from playwright.async_api import async_playwright


async def main():
    async with async_playwright() as p:
        browser = await p.chromium.launch(headless=False)
        page = await browser.new_page()
        await page.goto("www.baidu.com")
        await browser.close()


asyncio.run(main())
```

## 二、playwright 基础操作

### 1. 窗口最大化

设置args参数 `--start-maximized` 并且设置 `no_viewport=True`

```python
from playwright.sync_api import sync_playwright

browser_args = list()
browser_args.append("--start-maximized")

with sync_playwright() as p:
    # 启动浏览器
    browser = p.chromium.launch(headless=False, args=browser_args)
    # 创建上下文
    context = browser.new_context(no_viewport=True)
    # 创建标签页
    page = context.new_page()
    # 打开指定网站
    page.goto("https://www.baidu.com")
    # 关闭浏览器
    browser.close()
```

### 2. 设置窗口的指定大小

```python
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    # 启动浏览器
    browser = p.chromium.launch(headless=False)
    # 创建上下文
    context = browser.new_context(viewport={'width': 1920, 'height': 1080})
    # 创建标签页
    page = context.new_page()
    # 打开指定网站
    page.goto("https://www.baidu.com")
    # 关闭浏览器
    browser.close()
```

### 3. 启动本地浏览器

启动Google Chrome 浏览器的时候默认用的是无痕模式，不加载本地的数据，如果需要加载本地数据的话，就要使用` launch_persistent_context `

```python
import getpass

from playwright.sync_api import sync_playwright

browser_args = list()
browser_args.append("--start-maximized")


# 获取 google chrome 的本地缓存文件
USER_DIR_PATH = f"C:\\Users\\{getpass.getuser()}\\AppData\Local\Google\Chrome\\User Data"


with sync_playwright() as p:
    context = p.chromium.launch_persistent_context(
        # 指定本机用户缓存地址
        user_data_dir=USER_DIR_PATH,
        # 接收下载事件
        accept_downloads=True,
        # 设置 GUI 模式
        headless=False,
        # 允许绕过内容安全策略
        bypass_csp=True,
        # 减慢Playwright执行(毫秒)
        slow_mo=1000,

    )
    page = context.new_page()

    page.pause()
    page.close()
```

### 4. 在打开的浏览器上继续操作

首先需要设置浏览器的环境变量，通过cmd 可以直接打开浏览器。

打开cmd输入命令启动chrome浏览器

- `--remote-debugging-port`  是指定运行端口，只要没被占用就行

- ` --user-data-dir ` 指定运行浏览器的运行数据，新建一个干净目录，不影响系统原来的

  数据

- `--incognito` 隐私模式打开

- `-–start-maximized` 窗口最大化

- `--new-window` 直接打开网址

```cmd
chrome.exe --remote-debugging-port=12345 --user-data-dir="D:\playwright_chrome

chrome.exe --remote-debugging-port=12345 --incognito --start-maximized --user-data-dir="D:\demo" --new-window https://www.baidu.com
```

**playwright 接管接管页面面**

```python
from playwright.sync_api import sync_playwright


with sync_playwright() as p:
    browser = p.chromium.connect_over_cdp('http://localhost:12345/')
    # 获取page对象
    page = browser.contexts[0].pages[0]
```

## 三、元素定位

css 选择器， xpath 语法定位， text 文本选择器， 组合定位

### 1. CSS 或 XPath 选择器

的前缀 `css=` 和 `xpath=` 是对定位方式的声明，不写也是可以的（**不建议！！**），playwright能够自动判断你写的是css还是xpath语法。

```python
# CSS and XPath
page.fill('css=#kw', "playwright")
page.fill('xpath=//*[@id="kw"]', "playwright")
```

### 2. 文本选择器

文本选择器是一个非常实用的定位方式，根据页面上看到的text文本就可以定位了，比如我们经

常使用xpath 的文本选择器定位

- 完全匹配文本 `//*[text()="百度百科"]`
- 包含某个文本 `//*[contains(text(),"百度百科")]`

playwright 封装了text文本定位的方式，也可以支持2种文本定位方式

```python
# text=hello world 没有加引号（单引号或者双引号），模糊匹配，对大小写不敏感
page.click("text=hello world")

# 有引号，精确匹配，对大小写敏感
page.click("text='Hello World'")
```

text 文本除了可以定位 a 标签，还可以定位 **button 按钮** 或者 **input 输入框**

```html
<input type="submit" id="su" value="百度一下" class="bg s_btn btn_h btnhover">
```

```python
page.click('text=百度一下')
```

### 3. 组合定位

不同的selector可组合使用，用 `>>` 连接

**css 与 xpath 组合**

```python
page.locator("#login-form >> //*[@id='loginBtn']").click()

# 等价于

page.locator("#login-form").locator("//*[@id='loginBtn']").click()
```

**css与text组合**

```python
page.locator("#login-form >> text=立即登录").click()

# 等价于

page.locator("#login-form").locator("text=立即登录").click()
```

### 4. playwright内置定位器

```python
page.get_by_text() #通过文本内容定位。
page.get_by_label() #通过关联标签的文本定位表单控件。
page.get_by_placeholder() #按占位符定位输入。
page.get_by_title() #通过标题属性定位元素。
page.get_by_role() #通过显式和隐式可访问性属性进行定位。
page.get_by_alt_text() #通过替代文本定位元素，通常是图像。
page.get_by_test_id() #根据 data-testid 属性定位元素（可以配置其他属性）
```

**实例**

```python
page.get_by_label("User Name").fill("John")
page.get_by_label("Password").fill("secret-password")
page.get_by_role("button", name="Sign in").click()
expect(page.get_by_text("Welcome, John!")).to_be_visible()
```

#### 4.1 page.get_by_text()

设置精确匹配

```python
page.get_by_text("Welcome, John", exact=True)
```

与正则表达式匹配

```python
page.get_by_text(re.compile("welcome, john", re.IGNORECASE))
```

#### 4.2 page.get_by_role()

```html
<button>Sign in</button>
```

```python
page.get_by_role("button", name="Sign in").click()
```

**注意：**

在HTML和Web可访问性（Accessibility，简称A11y）领域，许多元素确实具有隐式定义的角色（role）。这些角色帮助辅助技术（如屏幕阅读器）理解页面上的元素是什么，以及它们应该如何被用户交互。

 ```html
 <button>点击我</button>
 ```

在这个例子中，`<button>`元素创建了一个按钮，其角色隐式地被定义为`button`。用户可以通过点击或使用键盘（通常是空格键或回车键）来激活它。

然而，如果你使用了一个非标准的元素来创建一个看起来像按钮的控件，那么你就需要使用`role="button"`来显式地声明它的角色，以确保辅助技术能够正确地识别和处理它。例如：

```html
<div role="button" tabindex="0">点击我</div>
```

在这个例子中，`<div>`元素被赋予了`button`角色，并通过`tabindex="0"`使其可以通过键盘聚焦，从而使其表现得像一个真正的按钮。这是必要的，因为`<div>`本身并没有隐式的`button`角色

#### 4.3 page.get_by_alt_text()

```html
<img alt="playwright-logo" src="/img/playwright" width='200'>
```

```python
page.get_by_alt_text("playwright logo").click()
```



### 5. ElementHandle元素句柄

ElementHandle 表示页内 DOM 元素。ElementHandles 可以使用`page.query_selector()`方法创建。

ElementHandle 实例可以用作`page.eval_on_selector()`和`page.evaluate()`方法中的参数

```python
href_element = page.query_selector("a")
href_element.click()
```

**句柄指向页面上的特定 DOM 元素。如果那个元素改变了文本或者被 React 用来渲染一个完全不同的组件，句柄仍然指向那个 DOM 元素。这可能会导致意外行为。**

 **selenium 采用的是 http 协议，获取的元素句柄是固定的，不能实时去获取页面上的元素。**

**playwright 采用的是webscoket 协议，可以实时去获取页面元素，当DOM结构有更新的时候，也能重新获取到。**





## 四、元素操作

元素操作有两种方法

一种是先定位再操作，另一种是直接调用方法并传入定位

```python
# 方式一
page.locator('#kw').fill("playwright")

# 方式二
page.fill('#kw', "playwright")
```



### 1. 输入文字

#### 1.1 fill()

使用 locator.fill() 是填写表单字段的最简单方法。它聚焦元素并input使用输入的文本触发事件。它适用于 `<input>` , `<textarea>` 和 `[contenteditable]` 元素。

```python
# Text 文本框输入
page.get_by_role("textbox").fill("Peter")
# 根据label 定位 Date 日期输入
page.get_by_label("Birth date").fill("2020-02-02")
# Time input
page.get_by_label("Appointment time").fill("13:15")
# Local datetime input
page.get_by_label("Local time").fill("2020-03-02T05:15")	
```

#### 1.2 type()

一个字符一个字符地输入字段，就好像它是一个使用 `locator.type()` 的真实键盘的用户。

```python
# delay：按键之间的等待时间（毫秒）。默认为0。

page.locator("#kw").type("hello world", delay=100)
```



### 2. 点击

最长时间（毫秒），默认为30秒，传递“0”可禁用超时。默认值可以更改
通过使用 `browser_context.set_default_timeout()` 或 `page.set_defaulttimeout()` 方法。

```python
# 单击
page.get_by_role("button").click()

# 双击
page.get_by_text("Item").dblclick()

# 右击
page.get_by_text("Item").click(button="right")

# Shift + click
page.get_by_text("Item").click(modifiers=["Shift"])


# Click the top left corner
page.get_by_text("Item").click(position={ "x": 0, "y": 0})
```

相对于**selenium** 中`click()` 方法的优化

- 等待具有给定选择器的元素出现在 DOM 中
- 等待它显示出来，即不为空，不display:none，不visibility:hidden
- 等待它停止移动，例如，直到 css 转换完成
- 将元素滚动到视图中 
- 等待它在动作点接收指针事件，例如，等待直到元素变得不被其他元素遮挡



### 3. 悬停

```python
# Hover over element
page.get_by_text("Item").hover()
```



### 4. iframe

#### 4.1 iframe定位

可以使用 `page.frame_locator()` 或 `locator.frame_locator()` 方法创建 FrameLocator 捕获足该 iframe 中检索和定位元素。

iframe 定位器是**严格**的。这意味着如果有多个元素与给定的选择器匹配，则对 iframe 定位器的所有操作都会抛出异常。

```python
locator = page.frame_locator("my-frame").get_by_text("Submit")
locator.click()
```

还有一种frame() 定位方法 ，可以根据`name`属性和`url`属性匹配

```python
frame = page.frame(name="frame-name")
frame = page.frame(url=r".*domain.*")
```



#### 4.2 案例

以163 邮箱为例

```python
from playwright.sync_api import sync_playwright

browser_args = list()
browser_args.append("--start-maximized")

with sync_playwright() as p:
    # 启动浏览器
    browser = p.chromium.launch(headless=False, args=browser_args, slow_mo=1000)
    # 创建标签页
    page = browser.new_page(no_viewport=True)
    # 打开指定网站
    page.goto("https://mail.163.com/")

    iframe = page.frame_locator('xpath=//iframe[contains(@id, "x-URS-iframe")]')
    iframe.get_by_placeholder("邮箱账号或手机号码").fill("xxxxx")
    iframe.locator("xpath=//input[@id='pwdtext']").fill("xxxxx")

    page.pause()
    # 关闭浏览器
    browser.close()
```

#### 4.3 获取页面上所有的面上所有的iframe

```python
from playwright.sync_api import sync_playwright

browser_args = list()
browser_args.append("--start-maximized")

with sync_playwright() as p:
    # 启动浏览器
    browser = p.chromium.launch(headless=False, args=browser_args, slow_mo=1000)
    # 创建标签页
    page = browser.new_page(no_viewport=True)
    # 打开指定网站
    page.goto("https://mail.163.com/")

    for f in page.frames:
        print(f)
        
    page.pause()
    # 关闭浏览器
    browser.close()
```

相关操作

```python
# 当定位到有多个时候， last 将定位器返回到最后一个
frame_locator().last

# first 匹配第一个
frame_locator().first

# 使用index索引
frame_locator().nth(index)
```



### 5. 截图

playwright 除了可以截取当前屏幕，***还可以截长图，也可以对某个元素截图***

#### 5.1 screenshot 截图

```python
from playwright.sync_api import sync_playwright

browser_args = list()
browser_args.append("--start-maximized")

with sync_playwright() as p:
    # 启动浏览器
    browser = p.chromium.launch(headless=False, args=browser_args, slow_mo=1000)
    # 创建标签页
    page = browser.new_page(no_viewport=True)
    # 打开指定网站
    page.goto("https://www.baidu.com")
	
    # 图片存放位置为当前目录下
    page.screenshot(path="screenshot.png")
        
    page.pause()
    # 关闭浏览器
    browser.close()
```

#### 5.2 截长图

设置 `full_page=True` 参数 screenshot 是一个完整的可滚动页面的屏幕截图，就好像你有一个非常高的屏幕并且页面可以完全容纳它。

```python
page.screenshot(path="screenshot.png", full_page=True)
```

#### 5.3 截取单个元素

```python
from playwright.sync_api import sync_playwright

browser_args = list()
browser_args.append("--start-maximized")

with sync_playwright() as p:
    # 启动浏览器
    browser = p.chromium.launch(headless=False, args=browser_args, slow_mo=1000)
    # 创建标签页
    page = browser.new_page(no_viewport=True)
    # 打开指定网站
    page.goto("https://www.baidu.com")
	
    # 截取百度首页的输入框和查询按钮
    page.locator("xpath=//form[@id='form']").screenshot(path="test.png")
        
    page.pause()
    # 关闭浏览器
    browser.close()
```
