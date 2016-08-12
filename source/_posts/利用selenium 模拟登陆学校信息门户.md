layout: post
title: 利用selenium 模拟登陆信息门户
date: 2016-07-17 21:43:11
tags: selenium
comments: true
categories: code
---
**Selenium**是一种web自动化测试工具，兼容各大主流浏览器。之前写爬虫时总会遇到复杂验证机制的网站，往往费劲心思才能爬取到数据，这样人力和时间成本都十分大。有了selenium之后，利用其中的webdriver驱动浏览器，模拟用户点击操作，就可以登陆进目标网站，获取到信息了。


Python和Selenium的安装就跳过，有需要的读者自行Google安装，我们直接进入实战环节。作者采用的是Python2.7,Selenium2.53,环境为windows

首先，我们先了解Selenium的基本语法，首先定义一个webdriver驱动的浏览器，浏览器个人自行选择，这里使用Chrome来作为示范。
```

browser = webdriver.Chrome("chromedriver.exe")     
browser.set_window_size(1200, 900) 

```

这里则定义了一个webdriver驱动的Chrome浏览器。将浏览器的窗口大小定义为1200*900，以便观察。

**Ps:**在windows下需要在自己的项目中下载一个chromedriver.exe文件作为驱动，驱动正常安装的Chrome会报错，具体原因网上搜索好像是Selenium与Chrome的版本不兼容导致。chromedriver的下载地址[在这](http://chromedriver.storage.googleapis.com/index.html?path=2.7/)

```

browser.get('http://idas.uestc.edu.cn/authserver/login?service=http://portal.uestc.edu.cn/index.portal')

```

这里则是让我们驱动的浏览器访问到学校的信息门户登陆界面


这里会用到Chrome的F12，也就是开发人员工具，自行手动打开一个新的浏览器窗口后，进入到登陆界面，鼠标移动到用户名的窗口下，右击点击检查，就会看到这个表单的信息。


![](http://oayp1enl0.bkt.clouddn.com/selenium_uestc_login_f12.jpg)



我们可以看到，右方高亮区域中用户名的id为username,同理鼠标移动到下方密码窗口处，可以得到密码的id为password。

```

browser.find_element_by_id("username").send_keys("username")
time.sleep(0.5)
browser.find_element_by_id("password").send_keys("password")
time.sleep(1)

```

find_element_by_id是selenium中的一个定位元素的方法，帮助我们找到刚刚在浏览器中查看到的id

time.sleep则是让程序暂停一会，以便我们更好的观察、调试程序


下面是常用的定位元素方法

```

通过id方式定位


browser.find_element_by_id("kw").send_keys("selenium")

通过name方式定位


browser.find_element_by_name("wd").send_keys("selenium")

通过tag name方式定位


browser.find_element_by_tag_name("input").send_keys("selenium")

通过class name 方式定位


browser.find_element_by_class_name("s_ipt").send_keys("selenium")

通过CSS方式定位


browser.find_element_by_css_selector("#kw").send_keys("selenium")

通过xpath方式定位


browser.find_element_by_xpath("//input[@id='kw']").send_keys("selenium")

```

通过定位到登陆按钮，调用click，产生点击操作，就可以看到程序操纵的浏览器已经登陆进去了

```
browser.find_element_by_xpath('//*[@id="casLoginForm"]/p[4]/button').click()

```

同理，定位到教务系统按钮，点击进入


![](http://oayp1enl0.bkt.clouddn.com/selenium_uestc_jiaowu_f12.jpg)


点击教务系统时发现，切换界面时程序又打开了一个新的窗口，这时两个窗口，程序如何定位到我们想要的窗口呢？


```
now_handle = browser.current_window_handle
for handle in all_handles:
    if handle != now_handle:
        browser.switch_to.window(handle)
        time.sleep(2)
        browser.find_element_by_xpath('//*[@id="MLeft"]/div/ul/li[1]/a').click()

```

需要利用到selenium的handle方法，在登陆界面时，确定当前的窗口为now_handle

在出现了多个窗口后，遍历所有的窗口，当循环到不是之前定位的now_handle,便是新的窗口，将浏览器切换到新窗口，在定位元素，进行相应操作就可以了。

进入到课程表界面后，当我们想截屏保存课程的图片是可以调用selenium的get_screenshot_as_file方法，将当前浏览器的视图保存为图片存入。在自动化测试中，这样可以很方便的监控selenium浏览器的运行情况。


```
browser.get_screenshot_as_file('courseTable.jpg')

```


### 总结体会



selenium良好的兼容性、完善的功能，使得它可以使用在大规模的自动化测试中，特别是web开发测试。在爬虫中，同样可以用在一些验证机制复杂的网站，缺点是爬取速度慢


