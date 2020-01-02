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
