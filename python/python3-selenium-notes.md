# Python Selenium Notes

## Firefox Disabling Log File

When creating the service class that you pass into webdriver.Firefox(), 
assign `os.devnull` to log_path to disable geckodriver.log


```python
import os
from selenium import webdriver
from selenium.webdriver.firefox.service import Service as FirefoxService

service = FirefoxService(log_path=os.devnull)

browser = webdriver.Firefox(service=service)
```
