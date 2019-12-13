---
title: selenium入门基础
tags:
  - python
url: 115.html
id: 115
comments: false
categories:
  - 运维
date: 2016-11-30 09:37:47
---

# 安装及基本操作

    # -*- coding:utf-8 -*-
    # 环境: ubuntu14.04
    # pip install seleninum
    # firefox
    # https://github.com/mozilla/geckodriver/releases 下载对应版本放在$PATH目录下
    # google
    # https://sites.google.com/a/chromium.org/chromedriver/downloads 下载对应版本放在$PATH目录下
    # http://www.seleniumhq.org/download/    其他浏览器参考
    # sudo apt-get install phantomjs  安装无界面webkit浏览器引擎
    from selenium import webdriver
    from selenium.webdriver.common.by import By
    
    driver = webdriver.Firefox()
    # driver = webdriver.Chrome()
    # driver =  webdriver.PhantomJS(executable_path='/usr/bin/phantomjs')
    driver.get('http://www.baidu.com')
    
    # 定位元素
    # 根据id定位
    find_id = driver.find_element_by_id('wrapper')
    find_id = driver.find_element(By.ID, 'wrapper')
    # 根据name属性定位
    find_name = driver.find_element_by_name('wd')
    find_name = driver.find_element(By.NAME, 'wd')
    # 根据class定位
    find_class = driver.find_element_by_class_name('head_wrapper')
    find_class = driver.find_element(By.CLASS_NAME, 'head_wrapper')
    # 根据标签名称定位
    find_tag = driver.find_element_by_tag_name('div')
    find_tag = driver.find_element(By.TAG_NAME, 'div')
    # 根据链接文本定位
    find_a_text = driver.find_element_by_link_text('hao123')
    find_a_text = driver.find_element(By.LINK_TEXT, 'hao123')
    # 根据部分链接文本定位
    find_a_text = driver.find_element_by_partial_link_text('hao')
    find_a_text = driver.find_element(By.PARTIAL_LINK_TEXT, 'hao')
    # 根据绝对位置定位
    find_x_path = driver.find_elements_by_xpath('/html/body/div')
    find_x_path = driver.find_element(By.XPATH, '/html/body/div')
    # 支持层级，属性，逻辑运算符结合使用
    find_x_path = driver.find_element_by_xpath("//input[@id='kw']")  # // 当前目录下的某个目录下
    # Css定位
    find_css = driver.find_elements_by_css_selector('.s_ipt')
    find_css = driver.find_element(By.CSS_SELECTOR, '.s_ipt')
    
    # 设置浏览器宽高
    driver.set_window_size(480, 800)
    driver.get('http://www.126.com')
    # 控制浏览器前进倒退
    driver.back()
    driver.forward()
    # 刷新浏览器(F5)
    driver.refresh()
    # 当前页面
    print driver.current_url()
    # 元素操作
    # dom = driver.find_element(By.ID, 'kw')
    # dom.clear()  # 清除文本
    # dom.send_keys()  # 模拟键盘输入
    # dom.click()  # 点击元素
    # dom.submit()  # 模拟表单提交
    # dom.size()  # 返回元素的尺寸
    # dom.text()  # 返回元素的文本
    # dom.get_attribute(name)  # 返回元素的属性值
    # dom.is_displayed()  # 设置该元素是否可见
    
    # 鼠标操作
    from selenium.webdriver.common.action_chains import ActionChains
    driver.get('http://www.baidu.com')
    # context_click 鼠标右键点击
    right_click = driver.find_element_by_id('xx')
    # perform 执行所有ActionChains中存储的动作
    ActionChains(driver).context_click(right_click).perform()
    # above 鼠标悬停
    above = driver.find_element_by_id('xxx')
    ActionChains(driver).move_to_element(above).perform()
    # double_click 鼠标双击
    # drag_and_drop(source, target) 鼠标拖放
    
    
    # 键盘事件
    from selenium.webdriver.common.keys import Keys
    find_id.send_keys(Keys.BACK_SPACE) # 删除键
    find_id.send_keys(Keys.SPACE)  # 空格键
    find_id.send_keys(Keys.TAB)  # tab键
    find_id.send_keys(Keys.ESCAPE)  # ESC
    find_id.send_keys(Keys.ENTER)  # 回车键
    find_id.send_keys(Keys.CONTROL, 'c')  # 复制
    find_id.send_keys(Keys.CONTROL, 'v')  # 剪贴
    find_id.send_keys(Keys.F1) # F1
    
    # driver.quit()
    
    

# webdriver原理

    # -*- coding:utf-8 -*-
    from selenium import webdriver
    import logging
    
    logging.basicConfig(level=logging.DEBUG)
    driver = webdriver.Firefox()
    driver.get('http://www.baidu.com')
    driver.find_element_by_id('kw').send_keys('selenium')
    driver.find_element_by_id('su').click()
    driver.quit()
    

# 显示等待

    # -*- coding:utf-8 -*-
    # 显示等待
    from selenium import webdriver
    from selenium.webdriver.common.by import By
    from selenium.webdriver.support.ui import WebDriverWait
    from selenium.webdriver.support import expected_conditions as EC
    
    driver = webdriver.Firefox()
    driver.get('http://www.baidu.com')
    # 显示等待
    # 参数driver： 浏览器驱动
    # 参数timeout： 最大超时时间，单位秒
    # 参数poll_frequency： 检测的间隔时间，默认为0.5秒
    # 参数ignored_exceptions: 超时异常信息，默认为NoSuchElementException
    # WebDriverWait(driver, timeout, poll_frequency=0.5, ignored_exceptions=None)
    # until(method, message='') 调用该方法提供的驱动程序作为参数，直到返回True
    # until_not(method, message='') 调用该方法提供的驱动程序作为参数，直到返回False
    # EC.title_is 判断当前页面的标题是否是否等于预期
    # EC.title_contains 判断当前页面的标题是否包含预期的字符串
    # EC.presence_of_element_located 判断元素是否被加载在DOM树中，并不代表当前元素可见
    # EC.visibility_of_element_located 判断元素是否可见（可见并非代表元素非隐藏，并且元素的宽和高均不等于0）
    # EC.visibility_of 与visibility_of_element_located相同，该方法接受的参数为定位后的元素
    # EC.presence_of_all_elements_located 判断是否至少有一个元素存在与DOM树中
    # EC.text_to_be_present_in_element 判断某个元素的text是否包含预期的字符串
    # EC.text_to_be_present_in_element_value 判断某个元素的vaule属性是否包含了预期的字符串
    # EC.frame_to_be_available_and_switch_to_it 判断该表单是否可以切换进去，如果可以，返回True并switch进去，否则返回false
    # EC.invisibility_of_element_located 判断某个元素是否不存在于DOM树中或者不可见
    # EC.element_to_be_clickable 判断某个元素是否可以点击
    # EC.staleness_of 等到一个元素从DOM树中移除
    # EC.element_to_be_selected 判断某个元素是否被选中，一般用于下拉菜单
    # EC.element_selection_state_to_be 判断某个元素的选中状态是否符合预期, 参数为定位后的元素
    # EC.element_located_selection_state_to_be 判断某个元素的选中状态是否符合预期,该方法接受的参数为定位
    # EC.alert_is_present 判断页面上是否有alert
    # driver.find_element_by_id().is_displayed() 判断元素是否可见
    element = WebDriverWait(driver, 5, 0.5).until(EC.presence_of_element_located((By.ID, 'kw')))
    element.send_keys('selenium')
    # driver.quit()
    
    

# 隐式等待

    # -*- coding:utf-8 -*-
    # 隐式等待
    from selenium import webdriver
    from selenium.common.exceptions import NoSuchElementException
    from time import ctime
    from time import sleep
    driver = webdriver.Firefox()
    # sleep(10)
    driver.implicitly_wait(10)
    driver.get('http://www.baidu.com')
    print ctime()
    try:
        driver.find_element_by_id('kw22').send_keys('selenium')
    except NoSuchElementException as e:
        print e
    finally:
        print ctime()
    

# 执行js代码

    # -*- coding:utf-8 -*-
    # 执行js代码
    from selenium import webdriver
    from time import sleep
    
    # 访问百度
    driver = webdriver.Firefox()
    driver.get('http://www.baidu.com')
    driver.set_window_size(600, 600)
    driver.find_element_by_id('kw').send_keys('selenium')
    driver.find_element_by_id('su').click()
    sleep(2)
    
    js = 'window.scrollTo(100, 450);'
    driver.execute_script(js)
    sleep(3)
    
    # driver.quit()
    

# 处理cookie

    # -*- coding:utf-8 -*-
    # cookie处理
    from selenium import webdriver
    driver = webdriver.Firefox()
    driver.get('http://www.youdao.com')
    driver.add_cookie({'name': 'name-a', 'value': 'value-b'})
    # 获取cookie信息
    cookies = driver.get_cookies()
    print cookies
    for cookie in cookies:
        print cookie
        print '{} -> {}'.format(cookie['name'], cookie['value'])
    driver.quit()
    

# 截图

    # -*- coding:utf-8 -*-
    # 窗口截图
    from selenium import webdriver
    from time import sleep
    
    # driver = webdriver.Firefox()
    driver =  webdriver.PhantomJS(executable_path='/usr/bin/phantomjs')
    driver.get('http://www.baidu.com')
    
    driver.find_element_by_id('kw').send_keys('selenium')
    driver.find_element_by_id('su').click()
    sleep(2)
    
    driver.get_screenshot_as_file('./screen.png')
    driver.quit()
    

# 播放视频

    # -*- coding:utf-8 -*-
    from selenium import webdriver
    from time import sleep
    
    driver = webdriver.Firefox()
    driver.get('http://videojs.com')
    
    video = driver.find_element_by_xpath('body/Setion[1]/div/video')
    # 返回播放文件地址
    url = driver.execute_script('return arguments[0].currentSrc;', video)
    print url
    
    # 播放视频
    print 'start'
    driver.execute_script('return arguments[0].play()', video)
    
    # 播放15秒
    sleep(15)
    
    # 暂停播放
    print 'stop'
    driver.execute_script('arguments[0].pause()', video)
    
    # driver.quit()