
# Web Scraping

### Introduction

The web is full of great datasets, but not all of them are readily available for download and analysis.
Today we'll take a look at how you can surf the web robotically, saving the relevant information into storage containers as you go!

We'll be taking a look at a few packages:
* Beautiful Soup
* Selenium
* re
* Pandas

Our general approach will be:
* pick a domain/set of web pages to scrape
* investigate those web pages using the developers tools from your web browser (such as Chrome or Firefox)
* write rules to select the relevant objects from the DOM
* parse information from those objects and store it in a container


```python
from bs4 import BeautifulSoup
import requests
import selenium
import re
import pandas as pd
```

### Web Page Introduction: **The DOM**

Before we start scraping, having a little background about how web pages are formatted is very helpful.

"The Document Object Model (DOM) is a programming interface for HTML and XML documents. It represents the page so that programs can change the document structure, style, and content. The DOM represents the document as nodes and objects. That way, programming languages can connect to the page."

https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model/Introduction

### An example webpage


```python
#How complex or simple should this be? Is this lesson going to be overwhelming?
```

### Grabbing a Web Page


```python
html_page = requests.get('https://www.azlyrics.com/') #Make a get request to retrieve the page
soup = BeautifulSoup(html_page.content, 'html.parser') #Pass the page contents to beautiful soup for parsing
```


```python
#Preview the soup....MMMMMM 
soup.prettify()[:1000]
```




    '<!DOCTYPE html>\n<html lang="en">\n <head>\n  <meta charset="utf-8"/>\n  <meta content="IE=edge" http-equiv="X-UA-Compatible"/>\n  <meta content="width=device-width, initial-scale=1" name="viewport"/>\n  <meta content="noarchive" name="robots"/>\n  <meta content="AZLyrics" name="name"/>\n  <meta content="lyrics,music,song lyrics,songs,paroles" name="keywords"/>\n  <base href="//www.azlyrics.com"/>\n  <script src="//www.azlyrics.com/external.js" type="text/javascript">\n  </script>\n  <title>\n   AZLyrics - Song Lyrics from A to Z\n  </title>\n  <link href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.4/css/bootstrap.min.css" rel="stylesheet"/>\n  <link href="//www.azlyrics.com/bsaz.css" rel="stylesheet"/>\n  <!-- HTML5 shim and Respond.js for IE8 support of HTML5 elements and media queries -->\n  <!--[if lt IE 9]>\r\n      <script src="https://oss.maxcdn.com/html5shiv/3.7.2/html5shiv.min.js"></script>\r\n      <script src="https://oss.maxcdn.com/respond/1.4.2/respond.min.js"></script>\r\n    <![endif]-->\n  <'




```python
#Get all the hyperlinks on a page
soup.findAll('a')
```




    [<a class="navbar-brand" href="//www.azlyrics.com"><img alt="AZLyrics.com" class="pull-left" src="//www.azlyrics.com/az_logo_tr.png" style="max-height:40px; margin-top:-10px;"/></a>,
     <a class="btn btn-menu" href="//www.azlyrics.com/a.html">A</a>,
     <a class="btn btn-menu" href="//www.azlyrics.com/b.html">B</a>,
     <a class="btn btn-menu" href="//www.azlyrics.com/c.html">C</a>,
     <a class="btn btn-menu" href="//www.azlyrics.com/d.html">D</a>,
     <a class="btn btn-menu" href="//www.azlyrics.com/e.html">E</a>,
     <a class="btn btn-menu" href="//www.azlyrics.com/f.html">F</a>,
     <a class="btn btn-menu" href="//www.azlyrics.com/g.html">G</a>,
     <a class="btn btn-menu" href="//www.azlyrics.com/h.html">H</a>,
     <a class="btn btn-menu" href="//www.azlyrics.com/i.html">I</a>,
     <a class="btn btn-menu" href="//www.azlyrics.com/j.html">J</a>,
     <a class="btn btn-menu" href="//www.azlyrics.com/k.html">K</a>,
     <a class="btn btn-menu" href="//www.azlyrics.com/l.html">L</a>,
     <a class="btn btn-menu" href="//www.azlyrics.com/m.html">M</a>,
     <a class="btn btn-menu" href="//www.azlyrics.com/n.html">N</a>,
     <a class="btn btn-menu" href="//www.azlyrics.com/o.html">O</a>,
     <a class="btn btn-menu" href="//www.azlyrics.com/p.html">P</a>,
     <a class="btn btn-menu" href="//www.azlyrics.com/q.html">Q</a>,
     <a class="btn btn-menu" href="//www.azlyrics.com/r.html">R</a>,
     <a class="btn btn-menu" href="//www.azlyrics.com/s.html">S</a>,
     <a class="btn btn-menu" href="//www.azlyrics.com/t.html">T</a>,
     <a class="btn btn-menu" href="//www.azlyrics.com/u.html">U</a>,
     <a class="btn btn-menu" href="//www.azlyrics.com/v.html">V</a>,
     <a class="btn btn-menu" href="//www.azlyrics.com/w.html">W</a>,
     <a class="btn btn-menu" href="//www.azlyrics.com/x.html">X</a>,
     <a class="btn btn-menu" href="//www.azlyrics.com/y.html">Y</a>,
     <a class="btn btn-menu" href="//www.azlyrics.com/z.html">Z</a>,
     <a class="btn btn-menu" href="//www.azlyrics.com/19.html">#</a>,
     <a href="/d/dancegavindance.html#56023"><img alt="" src="/hot/56023.jpg"/></a>,
     <a href="/d/dancegavindance.html#56023">Dance Gavin Dance</a>,
     <a href="/d/davematthewsband.html#56994"><img alt="" src="/hot/56994.jpg"/></a>,
     <a href="/d/davematthewsband.html#56994">Dave Matthews Band</a>,
     <a href="/b/bentley.html#52994"><img alt="" src="/hot/52994.jpg"/></a>,
     <a href="/b/bentley.html#52994">Dierks Bentley</a>,
     <a href="/l/lilyallen.html#55106"><img alt="" src="/hot/55106.jpg"/></a>,
     <a href="/l/lilyallen.html#55106">Lily Allen</a>,
     <a href="/n/neyo.html#57858"><img alt="" src="/hot/57858.jpg"/></a>,
     <a href="/n/neyo.html#57858">Ne-Yo</a>,
     <a href="/g/ghostbc.html#56203"><img alt="" src="/hot/56203.jpg"/></a>,
     <a href="/g/ghostbc.html#56203">Ghost</a>,
     <a href="/w/west.html#57991"><img alt="" src="/hot/57991.jpg"/></a>,
     <a href="/w/west.html#57991">Kanye West</a>,
     <a href="/o/owlcity.html#51207"><img alt="" src="/hot/51207.jpg"/></a>,
     <a href="/o/owlcity.html#51207">Owl City</a>,
     <a href="/l/lauv.html#57844"><img alt="" src="/hot/57844.jpg"/></a>,
     <a href="/l/lauv.html#57844">Lauv</a>,
     <a href="/c/chvrches.html#53546"><img alt="" src="/hot/53546.jpg"/></a>,
     <a href="/c/chvrches.html#53546">CHVRCHES</a>,
     <a href="/p/pushat.html#43114"><img alt="" src="/hot/43114.jpg"/></a>,
     <a href="/p/pushat.html#43114">Pusha T</a>,
     <a href="/s/shawnmendes.html#56754"><img alt="" src="/hot/56754.jpg"/></a>,
     <a href="/s/shawnmendes.html#56754">Shawn Mendes</a>,
     <a href="//www.azlyrics.com/add.php">Submit Lyrics</a>,
     <a href="//www.stlyrics.com">Soundtracks</a>,
     <a href="//www.facebook.com/pages/AZLyricscom/154139197951223">Facebook</a>,
     <a href="//www.azlyrics.com/contact.html">Contact Us</a>,
     <a href="//www.azlyrics.com/adv.html">Advertise Here</a>,
     <a href="//www.azlyrics.com/privacy.html">Privacy Policy</a>,
     <a href="//www.azlyrics.com/copyright.html">DMCA Policy</a>]



### Parsing the DOM

### Cleaning Elements

### Storing Elements

### Visualizing Results

Beautiful Soup is a Python library designed for quick turnaround projects like screen-scraping. Three features make it powerful:

* Beautiful Soup provides a few simple methods and Pythonic idioms for navigating, searching, and modifying a parse tree: a toolkit for dissecting a document and extracting what you need. It doesn't take much code to write an application

* Beautiful Soup automatically converts incoming documents to Unicode and outgoing documents to UTF-8. You don't have to think about encodings, unless the document doesn't specify an encoding and Beautiful Soup can't detect one. Then you just have to specify the original encoding.

* Beautiful Soup sits on top of popular Python parsers like lxml and html5lib, allowing you to try out different parsing strategies or trade speed for flexibility.

Beautiful Soup parses anything you give it, and does the tree traversal stuff for you. You can tell it "Find all the links", or "Find all the links of class externalLink", or "Find all the links whose urls match "foo.com", or "Find the table heading that's got bold text, then give me that text."

### Selenium

### Importing Selenium and Setting the driver


```python
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
```

### Download Chromedriver
https://sites.google.com/a/chromium.org/chromedriver/home


```python
cd /Users/matthew.mitchell/Documents/Tools/
```

    /Users/matthew.mitchell/Documents/Tools



```python
driver = webdriver.Firefox('/Users/matthew.mitchell/Documents/Tools/Scraping/geckodriver/')
driver.get("http://www.gmail.com")
```


    ---------------------------------------------------------------------------

    NotADirectoryError                        Traceback (most recent call last)

    <ipython-input-15-07a1575a2f89> in <module>()
    ----> 1 driver = webdriver.Firefox('/Users/matthew.mitchell/Documents/Tools/Scraping/geckodriver/')
          2 driver.get("http://www.gmail.com")


    ~/anaconda3/lib/python3.6/site-packages/selenium/webdriver/firefox/webdriver.py in __init__(self, firefox_profile, firefox_binary, timeout, capabilities, proxy, executable_path, options, log_path, firefox_options, service_args, desired_capabilities)
        145         if firefox_profile is not None:
        146             if isinstance(firefox_profile, basestring):
    --> 147                 firefox_profile = FirefoxProfile(firefox_profile)
        148             self.profile = firefox_profile
        149             options.profile = firefox_profile


    ~/anaconda3/lib/python3.6/site-packages/selenium/webdriver/firefox/firefox_profile.py in __init__(self, profile_directory)
         76             newprof = os.path.join(self.tempfolder, "webdriver-py-profilecopy")
         77             shutil.copytree(self.profile_dir, newprof,
    ---> 78                             ignore=shutil.ignore_patterns("parent.lock", "lock", ".parentlock"))
         79             self.profile_dir = newprof
         80             os.chmod(self.profile_dir, 0o755)


    ~/anaconda3/lib/python3.6/shutil.py in copytree(src, dst, symlinks, ignore, copy_function, ignore_dangling_symlinks)
        307 
        308     """
    --> 309     names = os.listdir(src)
        310     if ignore is not None:
        311         ignored_names = ignore(src, names)


    NotADirectoryError: [Errno 20] Not a directory: '/Users/matthew.mitchell/Documents/Tools/Scraping/geckodriver/'


### Inspecting the Web Page for Relevant Elements

Right click (option click on mac) on an element you want to robotically interact with and go to inspect.
![](./inspect.png)

You should see the following pane open up displaying the underlying DOM.


![](./inspect2.png)

### Table of Top Commands/Methods

http://akul.me/blog/2016/selenium-cheatsheet/

### Scrolling + Javascript Snippets

### Summary
You should now have a brief intro to web scraping! The possabilities are nearly endless with what you can do. That said, not all websites will be thrilled with your new prowess. Surfing the web at super human speeds will get you banned from many domains and may violate the terms & conditions of many websites that require login. As such, there are a few considerations you should take along your way.

* Is there a terms and conditions for using the website?
* Test your scraping bot on in small samples to debug before scaling to hundreds, thousands or millions of requests.
* Start thinking about your IP address: getting blacklisted from a website is no fun. Consider using a VPN.
* Slow your bot down! Add delays along the way with the time package. Specifically, time.sleep(seconds) adds wait time in a program.

# Resources

#### Beatiful Soup - a good go to tool for parsing the DOM
https://www.crummy.com/software/BeautifulSoup/?

#### Selenium - Browser automation
https://www.seleniumhq.org/

#### Scrapy - another package for scraping larger datasets at scale
https://scrapy.org/
