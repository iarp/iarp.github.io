# Python 3, Selenium with PhantomJS, Chrome, or Firefox

> Originally Written: 2017-08-17

One of the many things I do programming wise is interacting with other websites and scraping data or submitting forms. 
Many websites these days use a lot of javascripting for authentication purposes or just the entire website in 
general (think Angular).

If you need or want to scrape a website with data, or perhaps submit a form that is hidden behind a login screen, 
Selenium is a good alternative to something like Requests and BeautifulSoup. It allows you to interact with the 
browsers installed on your computer (Chrome, Firefox ...etc).

```python
from selenium import webdriver
```

Firefox:

```python
browser = webdriver.Firefox(executable_path='C:/apps/geckodriver.exe')
```

Chrome:

```python
browser = webdriver.Chrome('C:/apps/chromedriver.exe')
```

PhantomJS (headless browser)

```python
browser = webdriver.PhantomJS('C:/apps/phantomjs.exe')
```

## Interacting with the browser

PhantomJS is a headless browser. It is capable of visiting a site, processing javascript as required all without 
the need of controlling an actual browser on your computer. As such you cannot see what is happening, where as with 
chrome and firefox you can actually see what its doing and what it sees while its doing whatever you tell it to do. 
You can program and debug/test using Chrome or Firefox all you want and then switch to PhantomJS whenever you want, 
they typically all work the same way.

The rest of selenium is browser independent

### Loading a website

```python
browser.get('https://www.google.ca')
```

### Finding an element (by name or id) and filling in some data, such as a username or password box

```python
username = browser.find_element_by_id('username')
# OR
username = browser.find_element_by_name('username')

username.send_keys('my username here')
```

### Finding a Select box (dropdown) and selecting an option by either value or visible text

```python
from selenium.webdriver.support.ui import Select

element = browser.find_element_by_id('user-type')
select_field = Select(element)

# To select by the option elements value="" value
select_field.select_by_value('Admin')

# To select by the visible text of the option element
select_field.select_by_visible_text('Administrator')
```
