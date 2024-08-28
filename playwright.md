- [一、启动浏览器](#一启动浏览器)
  -  [1. 同步方式（sync_api）](#1-同步方式sync_api)
  - [2. 异步方式（sync_api）](#2-异步方式sync_api)
- [二、playwright 基础操作](#二playwright-基础操作)
  - [1. 窗口最大化](#1-窗口最大化)
  - [2. 设置窗口的指定大小](#2-设置窗口的指定大小)
  - [3. 启动本地浏览器](#3-启动本地浏览器)
  - [4. 在打开的浏览器上继续操作](#4-在打开的浏览器上继续操作)
  - [5. new_context上下文](#5-new_context上下文)
  - [6. page.goto(url)生命周期](#6-pagegotourl生命周期)
    - [6.1 事件状态](#61-事件状态)
    - [6.2 案例](#62-案例)
    - [6.3 timeout等待超时](#63-timeout等待超时)
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
  - [6. 文件上传](#6-文件上传)
    - [6.1 input 文件输入框](#61-input-文件输入框)
    - [6.2 非input 文件输入框](#62-非input-文件输入框)
    - [6.3 事件监听filechooser](#63-事件监听filechooser)
  - [7. 文件下载](#7-文件下载)
    - [7.1 expect_download()](#71-expect_download)
    - [7.2 download相关操作](#72-download相关操作)
  - [8. 监听dialog处理alert](#8-监听dialog处理alert)
    - [8.1 dialog事件监听](#81-dialog事件监听)
    - [8.2 dialog属性和方法](#82-dialog属性和方法)
    - [8.3 案例](#83-案例)
  - [9. 定位多个元素](#9-定位多个元素)
  - [10. locator.filter()过滤定位器](#10-locatorfilter过滤定位器)
    - [10.1 has_tex参数](#101-has_tex参数)
    - [10.2 has参数](#102-has参数)
    - [10.3 链式定位](#103-链式定位)
  - [11. 无序列表(listitem)定位](#11-无序列表listitem定位)
  - [12. scroll滚动](#12-scroll滚动)
  - [13. 事件的监听与取消](#13-事件的监听与取消)
    - [13.1 等待特定事件](#131-等待特定事件)
    - [13.2 添加/删除事件](132-添加/删除事件)
  - [14. checkbox和radio相关操作](#14-checkbox和radio相关操作)
    - [14.1 radio单选操作](#141-radio单选操作)
    - [14.2 checkbox复选框](#142-checkbox复选框) 
  - [15. 切换标签页](#15-切换标签页)
  - [16. 处理新标签窗口](#16-处理新标签窗口)
    - [16.1 context.expect_page()](#161-context.expect_page)
    - [16.2 page.expect_popup()](#162-page.expect_popup)



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

### 5. new_context上下文

`browser.new_context()` 创建一个新的浏览器上下文。**它不会与其他浏览器上下文共享 cookies/缓存**。

如需要不同用户登录同一个网页，不需要创建多个浏览器实例，只需要创建多个context即可。

```python
from playwright.sync_api import sync_playwright, Dialog

browser_args = list()
browser_args.append("--start-maximized")

with sync_playwright() as p:
    # 启动浏览器
    browser = p.chromium.launch(headless=False, args=browser_args, slow_mo=500)

    # 第一个上下文
    context_first = browser.new_context(no_viewport=True)
    # 第二个上下文
    context_second = browser.new_context(no_viewport=True)
    
    # 操作第一个浏览器窗口
    page1 = context_first.new_page()
    page1.goto("https://www.baidu.com")

    # 操作第二个浏览器窗口
    page2 = context_second.new_page()
    page2.goto("https://www.baidu.com")

    page1.pause()
    page2.pause()
    # 关闭浏览器
    browser.close()
```

### 6. page.goto(url)生命周期

调用 page.goto(url) 后页面加载过程：

- page.url 设定新的 url
- document 文档内容通过网络加载并解析
-  page.on("domcontentloaded") 事件触发
- 执行页面的 js 脚本，页面执行一些脚本并加载 css 和图像等资源
- page.on("load") 事件触发
- 页面执行动态加载的脚本
-  `networkidle`当 500 毫秒内没有新的网络请求时触发

#### 6.1 事件状态

从源码可以看到 wait_until 等待的事件可以支持["commit", "domcontentloaded", "load", "networkidle"] 四个参数，默认是等待 load 触发。

```python
 def goto(
        self,
        url: str,
        *,
        timeout: typing.Optional[float] = None,
        wait_until: typing.Optional[
            Literal["commit", "domcontentloaded", "load", "networkidle"]
        ] = None,
        referer: typing.Optional[str] = None
    ) -> typing.Optional["Response"]:
```

#### 6.2 案例

```python
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.chromium.launch(headless=False, slow_mo=1000)
    context = browser.new_context(accept_downloads=True)
    page = context.new_page()
    page.goto("https://playwright.dev/")
    page.get_by_role("link", name="Community").click()
    page.pause()
    browser.close()
    

# 如果是按默认的等待 load 事件，会出现报错, 因为国内加载外部网站时速度很慢,静态资源30s内无法正常加载完成
    
result = next(iter(done)).result()
playwright._impl._api_types.TimeoutError: Timeout 30000ms exceeded.
=========================== logs ===========================
navigating to "https://playwright.dev/", waiting until "load"
============================================================


# 其实页面元素在 domcontentloaded 阶段就已经加载到元素了，那么不用等待到load

from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.chromium.launch(headless=False, slow_mo=1000)
    context = browser.new_context(accept_downloads=True)
    page = context.new_page()
    page.goto("https://playwright.dev/", wait_until="domcontentloaded")
    page.get_by_role("link", name="Community").click()
    page.pause()
    browser.close()
```

#### 6.3 timeout等待超时

timeout 参数可以设置页面加载超时时间, 默认是30秒， 传递“0”以禁用超时。

更改默认值可以使用 以下方法更改

- `browser_context.set_fault_navigation_timeout() `
- `browser_context.set_fault_timeout()`
- ` page.set_fault_navigation_timeout()`
- `page.set_fault_timeout()`



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

### 6. 文件上传

#### 6.1 input 文件输入框

您可以使用locator.set_input_files()方法选择要上传的输入文件。它期望第一个参数指向`<input>` 的输入元素"file"。数组中可以传递多个文件。

```python
# Select one file
page.get_by_label("Upload file").set_input_files('myfile.pdf')

# Select multiple files
page.get_by_label("Upload files").set_input_files(['file1.txt', 'file2.txt'])

# Remove all the selected files
page.get_by_label("Upload file").set_input_files([])

# Upload buffer from memory
page.get_by_label("Upload file").set_input_files(
        files=[
            {"name": "test.txt", "mimeType": "text/plain", "buffer": b"this is a test"}
        ],
    )
```

#### 6.2 非input 文件输入框

没有输入元素（它是动态创建的， 非input 类型的文件上传框）

```python
# 当点击Upload file按钮的时候，获取expect_file_chooser事件
with page.expect_file_chooser() as fc_info:
    page.get_by_label("Upload file").click()

file_chooser = fc_info.value
file_chooser.set_files("myfile.pdf")

# 返回与此文件选择器关联的输入元素。
file_chooser.element 
# 返回此文件选择器是否接受多个文件。
file_chooser.is_multiple() 
# 返回此文件选择器所属的页面
file_chooser.page 
```

#### 6.3 事件监听filechooser

 捕获 `page.on("filechooser")` 事件

```python
page.on("filechooser", lambda file_chooser: file_chooser.set_files(r"D:\tou.png"))
# 点击选择文件按钮，会触发 filechooser 事件
page.get_by_label("选择文件").click()
```



### 7. 文件下载

#### 7.1 expect_download()

当浏览器上下文关闭时，所有属于浏览器上下文的下载文件都会被删除。

```python
with page.expect_download() as download_info:
    page.get_by_text("Download file").click()
download = download_info.value
# wait for download to complete
path = download.path()
```



#### 7.2 download相关操作

```python
# 1.取消下载。如果下载已经完成或取消，则不会失败。成功取消后，download.failure()将解析为'canceled'.
download.cancel()

# 2.删除下载的文件, 将等待下载完成。
download.delete()

# 3.返回下载错误（如果有）将等待下载完成。
download.failure()

# 4.获取下载所属的页面。
download.page

# 5.下载路径 如果下载成功，则返回下载文件的路径。 下载的文件名是随机 GUID，使用download.suggested_filename获取建议的文件名。
download.path()

# 6. 将下载复制到用户指定的路径。在下载仍在进行时调用此方法是安全的。将等待下载完成。
download.save_as(path)

# 7.返回此下载的建议文件名。
download.suggested_filename

# 8.返回下载的 url。
download.url
```



### 8. 监听dialog处理alert

```html
<!--index.html-->

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>

</head>
<body>
    <input id = "alert" value = "alert" type = "button" onclick = "alert('你好');"/>
    <input id = "confirm" value = "confirm" type = "button" onclick = "confirm('世界');"/>
    <input id = "prompt" value = "prompt" type = "button" onclick = "var name = prompt('please write','hello world');"/>
</body>
</html>
```

playwright 框架可以监听dialog事件，不管你alert 什么时候弹出来，弹出何种类型，监听到事件就自动处理了

```python
# 所以当你在playwright 框架中，如果你点击页面上的alert弹窗框一瞬间就消失了

with sync_playwright() as p:
    # 启动浏览器
    browser = p.chromium.launch(headless=False, args=browser_args, slow_mo=2000)
    # 创建标签页
    page = browser.new_page(no_viewport=True)
    # 打开指定网站
    page.goto("E:\\Python Project\\uiautotest_playwright\\index.html")

    page.click("#alert") # 点击后playwright会自动处理掉这个alert弹出框
```

#### 8.1 dialog事件监听

```python
page.on("dialog", lambda dialog: dialog.accept())
```

**手动处理**

```python
from playwright.sync_api import sync_playwright

browser_args = list()
browser_args.append("--start-maximized")


with sync_playwright() as p:
    # 启动浏览器
    browser = p.chromium.launch(headless=False, args=browser_args, slow_mo=2000)
    # 创建标签页
    page = browser.new_page(no_viewport=True)
    # 打开指定网站
    # page.goto("https://www.baidu.com")

    page.goto("E:\\Python Project\\uiautotest_playwright\\index.html")

    page.click("#alert")

    def handle_dialog(dialog):
        """监听后处理"""
        print(dialog.message)
        dialog.dismiss()

    page.on("dialog", handle_dialog)

    page.pause()
    # 关闭浏览器
    browser.close()
```

#### 8.2 dialog属性和方法

**accept()。**

```python
# 当对话框被接受时返回
dialog.accept()
dialog.accept(**kwargs)

# 参数 prompt_text(可选), 要在提示中输入的文本。如果对话框 type 没有提示，则不会产生任何影响
```

**default_value**

```python
# 如果对话框是提示的，则返回默认提示值。否则，返回空字符串, 是给prompt 属性的input 使用的，返回输入框中的值
dialog.default_value
```

**dismiss() **

```python
# 关闭对话框
dialog.dismiss()
```

**message**

```python
# 获取对话框中显示的消息，默认显示在alert中的数据
dialog.message
```

**type**

```python
# 返回对话框的类型，可以是 alert , beforeunload , confirm 或 prompt 其中一个。
dialog.type
```

#### 8.3 案例

```python
from playwright.sync_api import sync_playwright, Dialog

browser_args = list()
browser_args.append("--start-maximized")


with sync_playwright() as p:
    # 启动浏览器
    browser = p.chromium.launch(headless=False, args=browser_args, slow_mo=2000)
    # 创建标签页
    page = browser.new_page(no_viewport=True)
    # 打开指定网站
    # page.goto("https://www.baidu.com")

    page.goto("E:\\Python Project\\uiautotest_playwright\\index.html")

    def handle_dialog(dialog: Dialog, condition=True, text=None):
        """监听后处理"""
        print(f'dialog message: {dialog.message}')
        dialog_type = dialog.type
        if dialog_type == 'alert':
            dialog.accept()
        if dialog_type == 'confirm':
            if condition:
                dialog.accept()
            else:
                dialog.dismiss()
        if dialog_type == 'prompt':
            if text is None:
                dialog.dismiss()
            else:
                print(f'dialog message: {dialog.default_value}')
                dialog.accept(prompt_text=text)

    page.on("dialog", lambda dialog: handle_dialog(dialog, condition=True, text=str(2)))

    page.pause()
    # 关闭浏览器
    browser.close()
```

### 9. 定位多个元素

一般定位到页面上唯一的元素再进行操作，有时候一个元素的属性是一样的，会定位到多个元素

```html
<!--index.html-->

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>

</head>
<body>
<div>
    <label>标签：
        <a id="a1">旅游</a>
        <a id="a2">看书</a>
        <a id="a3">学习</a>
        <a id="a4">学python</a>
    </label>
</div>
</body>
</html>
```

```python
from playwright.sync_api import sync_playwright, Dialog

browser_args = list()
browser_args.append("--start-maximized")


with sync_playwright() as p:
    # 启动浏览器
    browser = p.chromium.launch(headless=False, args=browser_args, slow_mo=500)
    # 创建标签页
    page = browser.new_page(no_viewport=True)
    # 打开指定网站
    # page.goto("https://www.baidu.com")

    page.goto("E:\\Python Project\\uiautotest_playwright\\index.html")
	
    # 获取所有的元素，但是这个时候不是一个可以迭代的对象，需要使用playwrigt的内置方法进行操作
    elements = page.locator("//a")
    
    # 点第一个
    elements.first.click()
    
    # 点最后个
    elements.last.click()
    
    # nth() 根据索引定位
    elements.nth(0).click()
    
    # count 统计个数个数
    elements.count()
    
    # all() 将元素变成可迭代对象，进行批量操作
    for item in elements.all():
        print(item.text_content())

    page.pause()
    # 关闭浏览器
    browser.close()

```

### 10. locator.filter()过滤定位器

**支持通过链式的方式不断添加过滤条件**

`locator().filter()` 和 `locator().locator()` 有什么区别？

- `locator().locator()` 是从已经定位到的元素中，继续查询子元素或子孙元素。
- `locator().filter()` 是从已经定位到的元素中，根据选项缩小现有定位器的范围，可以按文本或定位器过滤。

```html
<!--index.html-->

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>

</head>
<body>
<div id="filter">
    <div class="item">
        <a href="">python1</a>
        <a href="">python2</a>
        <a href="">python3</a>
    </div>
    <div class="item">
        <a href="">playwright</a>
    </div>
    <div class="item">
        <a href="">selenium</a>
    </div>
    <div class="item">
        <a href="">demotest</a>
    </div>
</div>
</body>
</html>
```

#### 10.1 has_tex参数

匹配内部某处包含指定文本的元素，可能是在子元素或子元素中。

```python
# 可以单个过滤
elements = page.locator('xpath=//div[@class="item"]//a')
el = elements.filter(has_text="python1")
print(el.text_content())

# 通过连接的方式多次过滤
elements = page.locator('xpath=//div[@class="item"]//a')
el = elements.filter(has_text="p").filter(has_text='ght')
print(el.text_content())
```

#### 10.2 has参数

匹配包含与内部定位器匹配的元素的元素。根据外部定位器查询内部定位器。内部定位器是从外部定位器开始匹配的，而不是从文档根目录开始。

```python
elements = page.locator('xpath=//div[@class="item"]//a')
el = elements.filter(has=page.get_by_text("python"))
print(el.text_content())
```

#### 10.3 链式定位

```python
el = page.locator("xpath=//div[@class='item']").filter(has_text='py').filter(has_text='on').get_by_text("1")
print(el)
print(el.text_content())
```





### 11. 无序列表(listitem)定位

listitem 是无序列表，ul 和 li 标签组合。 **每一个 listitem 相当于 一对`<li></li>`标签**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>

</head>
<body>
<div>
    <ul>
        <li>
            <h3>Product 1</h3>
            <button id="myButton">Add to cart</button>
        </li>
        <li>
            <h3>Product 2</h3>
            <button>Add to cart</button>
        </li>
    </ul>
</div>
</body>
</html>

<script>
    document.getElementById('myButton').addEventListener('click', function() {
            console.log('这是一段要打印的话.');
    });
</script>
```

`listitem` 通常和 配合 `locator.filter()` 过滤选择器一起使用

```python
from playwright.sync_api import sync_playwright, Dialog

browser_args = list()
browser_args.append("--start-maximized")


with sync_playwright() as p:
    # 启动浏览器
    browser = p.chromium.launch(headless=False, args=browser_args, slow_mo=500)
    # 创建标签页
    page = browser.new_page(no_viewport=True)
    # 打开指定网站
    # page.goto("https://www.baidu.com")

    page.goto("E:\\Python Project\\uiautotest_playwright\\index.html")

    elements = page.get_by_role("listitem").all()
    for item in elements:
        if "Product 1" in item.inner_text():
            item.get_by_role("button").click()
    
    # 上面操作等价于下面的操作
    
    page.get_by_role("listitem").filter(has_text="Product 1").get_by_role(
        "button", name="Add to cart"
    ).click()

    page.pause()
    # 关闭浏览器
    browser.close()
```



### 12. scroll滚动

当页面超过屏幕的高度时候，需要滚动到元素出现的位置，让元素处于可视的窗口上才能去操作元素。**playwright 在操作元素的时候，都会自动去让元素出现在可视窗口, 大部分情况不需要我们去操。**

作滚动条

如果我们仅仅是让元素出现到窗口的可视范围，可以使用 `scroll_into_view_if_needed()`方法，它会让元素出现在屏幕的正中间

```python
from playwright.sync_api import sync_playwright

with sync_playwright() as pw:
    browser = pw.chromium.launch(headless=False, slow_mo=2000)
    page = browser.new_page()
    page.goto("https://www.runoob.com/")
    # 滚动元素到屏幕可视窗口
    page.get_by_text('【学习 Django】').scroll_into_view_if_needed()
```



### 13. 事件的监听与取消

#### 13.1 等待特定事件

**举两个例子：**

一、使用page.expect_request()等待具有指定 url 的请求：

```python
with page.expect_request("**/*logo*.png") as first:
	page.goto("https://v3.bootcss.com/")
print(first.value.url)
```

二、等待弹出新窗口：

```python
with page.expect_popup() as popup:
	page.get_by_text("xxxx").click()
popup.value.goto("https://v3.bootcss.com/")
```



#### 13.2 添加/删除事件

Page对象提供了一个on方法，用于监听页面上发生的各种事件。

具体支持的内置事件请参考官方文档 https://playwright.bootcss.com/docs/api/class-page

添加事件使用 `page.on('event', handle)`

删除事件使用 `page.remove_listener("event", handle_function)`

```python
def dialog_handle(dialog):
    dialog.dismiss()
   
# 添加监听
page.on("dialog", dialog_handle)

# 取消监听
page.remove_listener("dialog", dialog_handle)
```

**添加一次性事件**

如果某个事件需要处理一次，有一个方便的 API：`page.once()`

```python
page.once("dialog", lambda dialog: dialog.accept("2021"))
page.evaluate("prompt('Enter a number:')")
```



### 14. checkbox和radio相关操作

```python
locator.click() #点击操作
locator.check() #选中
locator.uncheck() #不选中
locator.set_checked() #设置选中状态
locator.is_checked() #判断是否被选中
```



```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>

</head>
<body>
<div>
    <label>性别：
        <input type="radio" name="sex" id="man" checked>男
        <input type="radio" name="sex" id="woman">女
    </label>
</div>
<div>
    <label>标签：
        <input type="checkbox" id="a1"> 旅游
        <input type="checkbox" id="a2">看书
        <input type="checkbox" id="a3" checked>学习
        <input type="checkbox" id="a4">学python
    </label>
</div>
</body>
</html>

```

#### 14.1 radio单选操作

```python
status1 = page.locator('#man').is_checked()
print(status1)

# 选择 女
page.locator('#woman').click()
print(page.locator('#woman').is_checked())

# 选择 女
page.locator('#woman').check()
print(page.locator('#woman').is_checked())


# 选择 女
page.locator('#woman').set_checked(checked=True)
print(page.locator('#woman').is_checked())

# 需注意的是，如果男本身就是选择状态，去设置unchecked 状态，会报错: Clicking thecheckbox did not change its state
```

#### 14.2 checkbox复选框

```python
# checkbox 操作
page.locator('#a1').click()
print(page.locator('#a1').is_checked())

# 如果想让元素必须是选择状态(不管之前有没被选中），可以使用check() 或 set_checked() 方法
page.locator('#a1').check()
print(page.locator('#a1').is_checked())

# set_checked() 方法
page.locator('#a1').set_checked(checked=True)
print(page.locator('#a1').is_checked())
```



 ### 15. 切换标签页

- `context.pages` 获取所有标签页

- `bring_to_front()` 激活指定标签页

```python
from playwright.sync_api import sync_playwright, Dialog

browser_args = list()
browser_args.append("--start-maximized")

with sync_playwright() as p:
    # 启动浏览器
    browser = p.chromium.launch(headless=False, args=browser_args, slow_mo=500)
    context = browser.new_context(no_viewport=True)
    # 创建标签页
    page = context.new_page()
    # 打开指定网站
    page.goto("https://www.baidu.com")

    # 点开多个标签页
    for link in page.locator('#s-top-left>a').all():
        link.click()

    for page_item in context.pages:
        if page_item.title() == "地图":
            # 激活当前选项卡
            page_item.bring_to_front()
           
    page.pause()
    # 关闭浏览器
    browser.close()

```



### 16. 处理新标签窗口

这个和 目录15 中切换标签的区别就是，切换标签后你只能在指定的一个页面上进行操作，通过下面的两个方法你可以随意的在多个页面中自由操作

#### 16.1 context.expect_page()

```python
from playwright.sync_api import sync_playwright

browser_args = list()
browser_args.append("--start-maximized")

with sync_playwright() as p:
    # 启动浏览器
    browser = p.chromium.launch(headless=False, args=browser_args, slow_mo=500)

    context = browser.new_context(no_viewport=True)

    # 操作第一个浏览器窗口
    page = context.new_page()
    page.goto("https://www.baidu.com")

    with context.expect_page() as new_page:
        page.click('text=新闻')

    page2 = new_page.value
    print(page2.title())

    page.pause()
    # 关闭浏览器
    browser.close()
```

#### 16.2 page.expect_popup()

```python
from playwright.sync_api import sync_playwright

browser_args = list()
browser_args.append("--start-maximized")

with sync_playwright() as p:
    # 启动浏览器
    browser = p.chromium.launch(headless=False, args=browser_args, slow_mo=500)

    context = browser.new_context(no_viewport=True)

    # 操作第一个浏览器窗口
    page = context.new_page()
    page.goto("https://www.baidu.com")

    with page.expect_popup() as new_page:
        page.click('text=新闻')

    page2 = new_page.value
    print(page2.title())

    page.pause()
    # 关闭浏览器
    browser.close()
```

