Selenium for Automated Testing of Web Applications
==================================================

```shell
$ pip install selenium
```

To use the webdriver Firefox with selenium you will need to install geckodriver which is available on github: https://github.com/mozilla/geckodriver/releases. A convenient location to store the geckodriver is `/usr/local/bin`

# Launching a Firefox Browser

```python
from selenium import webdriver

browser = webdriver.getFirefox()
browser.get(url)

# run bunch of tests...

browser.quit()
```

# Useful Functions for Accessing Elements
```python
>> print(browser.title) # window title of the web site
>>
>> element = browser.find_element_by_tag_name(tag_name)
>> print(element.text)
>>
>> elements = element.find_elements_by_tag_name(tag_name)
>> for e in elements:
>>     print(e.text)
>>
>> element = browser.find_element_by_id(element_id)
>> attr = element.get_attribute(attr_name)
>> print(attr)
```

# Entering Data into Input Boxes
```python
>> from selenium.webdriver.common.keys import Keys
>>
>> inputbox = browser.find_element_by_id(inputbox_id)
>> inputbox.send_keys(data)
>> inputbox.send_keys(Keys.ENTER)
```
