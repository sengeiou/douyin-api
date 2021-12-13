# 抖音数据采集教程，最全python库selenium自动化使用


### 一、安装selenium
```python
pip install Selenium
```

### 二、初始化浏览器

- Chrome 是初始化谷歌浏览器
- Firefox 是初始化火狐浏览器
- Edge 是初始化IE浏览器
- PhantomJS 是一个无界面浏览器。
```python
from selenium import webdriver
 
driver = webdriver.Chrome()
```
>**短视频直播数据采集接口SDK请**[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR) 


### 三、设置浏览器大小

-  maximize_window 最大化窗口
- set_window_size 自定义窗口大小
```python
from selenium import webdriver
 
driver = webdriver.Chrome()
driver.maximize_window()
```

### 四、访问页面
```python
from selenium import webdriver
 
driver = webdriver.Chrome()
 
driver.get('https://www.baidu.com')
```

### 五、定位元素

- 定位元素的基本方法如下




| 定位一个元素 | 定位多个元素 | 解释 |
| :--- | :--- | :--- |
| find_element_by_id | find_elements_by_id | 通过元素id定位 |
| find_element_by_name | find_elements_by_name | 通过元素name定位 |
| find_element_by_xpath | find_elements_by_xpath | 通过xpath表达式定位 |
| find_element_by_link_text | find_elements_by_link_tex | 通过完整超链接定位 |
| find_element_by_partial_link_text | find_elements_by_partial_link_text | 通过部分链接定位 |
| find_element_by_tag_name | find_elements_by_tag_name | 通过标签定位 |
| find_element_by_class_name | find_elements_by_class_name | 通过类名进行定位 |
| find_elements_by_css_selector | find_elements_by_css_selector | 通过css选择器进行定位 |



- **实例演示: ** **找到百度首页的输入框**
```python
from selenium import webdriver
 
driver = webdriver.Chrome()
driver.get('https://www.baidu.com')
driver.find_element_by_id('kw')
```

### 六、定位元素的另一种写法

- 需引入By模块
```python
from selenium import webdriver
from selenium.webdriver.common.by import By
 
driver = webdriver.Chrome()
driver.get('https://www.baidu.com')
driver.find_element(By.ID, 'kw')
```

### 七、元素交互
| **方法** | **解释** |
| :--- | :--- |
| click() | 点击某个元素 |
| send_keys(输入的值) | 模拟输入 |
| clear() | 清除操作 |
| submit() | 提交表单 |
| get_attribute(name) | 获取元素的属性值 |
| location | 获取元素的位置 |
| text | 获取元素的文本值 |
| size | 获取元素的大小 |
| id | 获取元素的id值 |
| tag_name | 获取元素的标签名 |

- **实例演示: ****在百度输入框中输入我是autofelix**，**并点击搜索按钮**
```python
from selenium import webdriver
 
driver = webdriver.Chrome()
driver.get('https://www.baidu.com')
driver.find_element_by_id('kw').send_keys('我是autofelix')
driver.find_element_by_id('su').click()
```

### 八、执行js
```python
from selenium import webdriver
 
driver = webdriver.Chrome()
driver.maximize_window()
 
driver.get('https://www.baidu.com')
 
js_sql = '''
    document.getElementById('kw').value = '我是autofelix'
'''
driver.execute_script(js_sql)
```

### 九、Frame操作

- 网页中frame的话，需要执行切入切出操作

- switch_to.from(子iframe的id名称) 切入

- switch_to.parent_frame(父iframe的id名称) 切出

```python
from selenium import webdriver
 
driver = webdriver.Chrome()
driver.maximize_window()
 
driver.get('https://www.baidu.com')
 
//这个网址并没有iframe,我臆测有,你们看看就行
driver.switch_to.frame('我臆测出来的iframe')
```

### 十、cookie的操作
| **方法** | **说明** |
| :--- | :--- |
| delete_all_cookies() | 删除当前页面所有cookies |
| get_cookie(name) | 获取指定cookie值 |
| get_cookies() | 获取当前页面所有cookies值 |
| add_cookie() | 设置cookie值 |

```python
from selenium import webdriver
 
driver = webdriver.Chrome()
driver.maximize_window()
 
driver.get('https://www.baidu.com')
 
driver.delete_all_cookies()
driver.add_cookie({'name': 'name', 'domain': '.baidu.com', 'value': 'autofelix'})
```

### 十一、选项卡管理
| **方法** | **解释** |
| :--- | :--- |
| window_handles | 保存所有选项卡的元组 |
| switch_to.window() | 切换选项卡 |

```python
from selenium import webdriver
 
driver = webdriver.Chrome()
driver.maximize_window()
 
driver.get('https://www.baidu.com')
driver.get('https://www.taobao.com')
 
driver.switch_to.window(driver.window_handles[0])
driver.switch_to.window(driver.window_handles[1])
```

### 十二、鼠标事件

- 鼠标事件需要引入`ActionChains模块`
| 方法 | 说明 |
| :--- | :--- |
| move_to_element(above) | 鼠标右击 |
| double_click() | 鼠标双击 |
| drag_and_drop() | 左键按住拖动 |
| perform() | 动作存储 |

- **实例演示: **滑动滑块验证码
```python
from selenium import webdriver
from selenium.webdriver import ActionChains
from selenium.webdriver.common.by import By
from selenium.webdriver.support.wait import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
 
# 初始化谷歌浏览器
driver = webdriver.Chrome()
 
# 最大化窗口
driver.maximize_window()
 
# 打开头条登陆网址
driver.get('https://sso.toutiao.com')
 
# 等待某个元素是否出现
WebDriverWait(self.driver, 10).until(
    EC.text_to_be_present_in_element((By.XPATH, '//*[@id="mobile-code-get"]/span'), u'发送')
)
 
# 实例化鼠标操作
action = ActionChains(self.driver)
 
# 按住滑块
action.click_and_hold(self.driver.find_element_by_xpath('//*[@id="captcha_container"]')).perform()
 
# 将滑块移动x的距离
action.move_by_offset(xoffset=x, yoffset=0).perform()
 
# 释放滑块
action.release().perform()
```

### 十三、等待

- 隐氏等待
- 如果到一定时间，指定的元素还没有出现，进程不会阻塞，但是到指定时间还没有找到，就会抛出异常
```python
from selenium import webdriver
 
driver = webdriver.Chrome()
driver.implicitly_wait(10)
 
driver.get('https://www.baidu.com')
```

- 显示等待
- 如果在一定时间内，指定的元素没有出现，进程会阻塞在这里，如果到指定时间还没有找到，就会抛出异常
```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
 
driver = webdriver.Chrome()
driver.implicitly_wait(10)
 
driver.get('https://www.baidu.com')
WebDriverWait(driver, 10).until(
    EC.presence_of_element_located((By.ID, 'kw'))
)
```

### 十四、前进、后退和刷新

- back 后退

- forward 前进

- refresh 刷新浏览器

```python
from selenium import webdriver
 
driver = webdriver.Chrome()
driver.get('https://www.baidu.com')
driver.get('https://www.taobao.com')
driver.get('https://www.jd.com')
 
driver.back()
driver.forward()
driver.refresh()
```

### 十五、关闭浏览器

- close 关闭当前标签页

- quit 关闭整个浏览器

```python
from selenium import webdriver
 
driver = webdriver.Chrome()
driver.get('https://www.baidu.com')
//打开百度页面后,关闭整个浏览器
driver.quit()
```
