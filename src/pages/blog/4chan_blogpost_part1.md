---
templateKey: blog-post
title: "Intro to the 4Chan JSON API"
date: 2016-12-17T15:04:10.000Z
featuredpost: false
featuredimage: /img/blog_post_cover.jpg
description: Data Minig 4chan with Python.
tags:
  - Python
  - 4chan
  - JSON
  - requests
  - /pol
  - Datamining
  - Datascience
---

For all the controversy surrounding 4chan since, well... the start of 4chan I suppose, im surprised at the lack of blog posts on their JSON API.

Yes! that's right, 4chan has a read only JSON API packed full of all kinds of de-moralizing, soul crushing possibilities.

Yet for whatever reason this goldmine of strange insights is almost non-existant in the commentaries of the internet.

If we search *'4chan JSON API python'* in duckduckgo or google, we get almost no blog post related hits.

In fact we get so few hits, one could get conspiratorial in coming up with reasons about how this could be.

The Infamous **/pol**
---
4chan is a very interesting place indeed.

You dont need to spend more than 3.5 minutes on the site to understand why its so controversial.

Probably the most famous and inflammatory message board on 4chan is /pol, or 'politically incorrect'.

For the past few years, I have been playing around with various social media api's for the purposes of natural language processing.

In this time I have read alot of comments from alot of different platforms, and 4chan is by far the most interesting and unique.

Moreover, before we begin I would like to clarify that this post is not about politics, and its not about judgement. This post is about python and JSON api's, that is all.


**4chan is a sub-culture**
---

4chan has its own terminology and value system.
For these reasons the natural language that exists in this very special place is one of a kind.

Im not saying its good, im saying I have not encountered it before in such mind numbing consistancy, on any other social media platform(reddit, twitter, facebook, etc).

Without getting into the specific language(you know what I mean, right?), from a machine learning perspective this is interesting to model.

But enough of my rambling lets get into it.

**References:**
---
- [requests](https://2.python-requests.org/en/master/)

- [requests: r.json()](https://2.python-requests.org/en/master/user/quickstart/#response-content)

- [4chan API Docs: https://github.com/4chan/4chan-API](https://github.com/4chan/4chan-API)

- [4chan API catalog endpoint Docs](https://github.com/4chan/4chan-API/blob/master/pages/Catalog.md)

- [enumerate()](https://www.python.org/dev/peps/pep-0279/)

- [range()](https://www.python.org/dev/peps/pep-0281/)

- [yield](https://www.python.org/dev/peps/pep-0255/)

- [.get(): method for extracting python dictionary keys without risk of a KeyError](https://docs.python.org/3.7/library/stdtypes.html#mapping-types-dict)

- [KeyErrors](https://docs.python.org/3/library/exceptions.html)

- [generator](https://docs.python.org/3/reference/expressions.html#yield-expressions) 

- [generator function](https://wiki.python.org/moin/Generators)

**Introduction:**
---

As of about 1 October 2019 the 4chan api team updated their docs on github, with some great documentation for all their JSON endpoints.

The endpoint we will be using is catalog: [https://a.4cdn.org/pol/catalog.json](https://a.4cdn.org/pol/catalog.json)

Now the tricky part of learning to parse any JSON api isn't necessarily the complexity of the code you have to write, but understanding the structure and relationships between the different field's and endpoints.

**Step 1:**
---

Import our imports.

```python
import requests
import json
from pprint import pprint
from datetime import datetime as dt
```

**Step 2:**
---
Establish a connection to our endpoint using the [requests](https://2.python-requests.org/en/master/) library.
```python
r = requests.get('https://a.4cdn.org/pol/catalog.json')
```

**Step 3:**
---
There are a couple of different methods for converting the response from the server.

Method #1:
```python
import json
import requests
from pprint import pprint

r = requests.get('https://a.4cdn.org/pol/catalog.json')
r = json.loads(r.text)

pprint(r)
```
Method #2:
```python
import requests
from pprint import pprint

r = requests.get('https://a.4cdn.org/pol/catalog.json')
r = r.json()

pprint(r)
```
Out:
```python
{'page': 11,
  'threads': [{'bumplimit': 0,
               'com': 'Thread OP'
               'country': 'US',
               'country_name': 'United States',
               'ext': '.png',
               'filename': 'picture.png',
               'fsize': 287315,
               'h': 1000,
               'imagelimit': 0,
               'images': 1,
               'last_modified': 1575651990,
               'last_replies': [{'com': 'Comment to Thread OP
                                 'country': 'US',
                                 'country_name': 'United States',
                                 'name': 'Anonymous',
                                {'com': '<a href="#p235356550" '
                                        'class="quotelink">&gt;&gt;235356550</a><br><span '
                                        'class="quote">&gtn</span>',
                                 'country': 'US',
                                 'country_name': 'United States',
                                 'ext': '.jpg',
                                 'filename': 'AnotherPic.jpg',
                                 'fsize': 65516,
                                 'h': 600,
                                 'id': '9PriyurA',
                                 'md5': 'UmA62vBYEKcDREnJDL+FCw==',
                                 'name': 'Anonymous',
                                 'tim': 1575651990509,
                                 'time': 1575651990,
                                 'tn_h': 125,
                                 'tn_w': 125,
                                 'w': 600}],
               'md5': 'rSv4H+6jrvu7zenf3gQ6DQ==',
               'name': 'Anonymous',
               'now': '12/06/19(Fri)11:25:54',
               'replies': 2,
               'resto': 0,
               'tn_h': 250,
               'tn_w': 225,
               'w': 900}]}]
```
As you can see, method 2 is the more computationally efficient solution, as it utilizes requests built in functionality, instead of having to import the json standard library. **Im using Method #2.**

**Step 4**: Access key/value pairs
---

Next step is to "parse" our data, meaning we need to re-arrange it in a way that makes it easy to process.

There are a few ways to do this, using pandas is my first instinct, but I find using standard library tools for this side of the ETL process the most practical.

The response object from [r.json()](https://2.python-requests.org/en/master/user/quickstart/#response-content) gives us a list of dictionaries, also known as **nested json**, which can be a real pain to sort out sometimes.
```python
import requests

r = requests.get('https://a.4cdn.org/pol/catalog.json')
r = r.json()

for items in r:
    print(items.keys())
    print(items['page'])
    print(items['threads'])

#print(items.keys())
dict_keys(['page', 'threads'])
dict_keys(['page', 'threads'])
dict_keys(['page', 'threads'])
dict_keys(['page', 'threads'])
dict_keys(['page', 'threads'])
dict_keys(['page', 'threads'])
dict_keys(['page', 'threads'])
dict_keys(['page', 'threads'])
dict_keys(['page', 'threads'])
dict_keys(['page', 'threads'])
dict_keys(['page', 'threads'])

#print(items['page'])
1
2
3
4
5
6
7
8
9
10
11

#print(items['threads'])
{'page': 11,
  'threads': [{'bumplimit': 0,
               'com': 'Thread OP Comment',
               'country': 'US',
               'country_name': 'United States',
               'ext': '.png',
               'filename': 'pic.png',
               'fsize': 287315,
               'h': 1000,
               'imagelimit': 0,
               'images': 1,
               'last_modified': 1575651990,
               'last_replies': [{'com': 'Comment to thread OP'
```
```python
import requests

r = requests.get('https://a.4cdn.org/pol/catalog.json')
r = r.json()

for items in r:
    for thread in items['threads']
        print(thread.keys())

#Out:
dict_keys(['no', 'now', 'name', 'sub', 'com', 'filename', 'ext', 'w', 'h', 'tn_w', 'tn_h', 'tim', 'time', 'md5', 'fsize', 'resto', 'id', 'country', 'bumplimit', 'imagelimit', 'semantic_url', 'country_name', 'replies', 'images', 'omitted_posts', 'omitted_images', 'last_replies', 'last_modified'])
```
There we go, now were getting to the good stuff.

We have successfully peeled back the outer layers of json, we now have access to the core json api data.

But there is just one problem, we are only getting the thread for page 1. What we want is to access all posts from all 11 pages simultaneously.


**Step 5**: Access all the pages and key/value fields with a single get request.
---
Let's recap what we learned in **Step 4** and develop a plan of action.

- We have 11 pages of threads at any given time for the entire /pol board.
- We have ~18-20 posts per page.
- Each page has it own page/thread key pair that must be accessed simultaneously, to access the data.

```plaintext
dict_keys(['page'])
            |
            | -- dict_key(['page==1', 'thread for page==1'])
            |                          |    
            |                          |
            |                          └───dict_keys(['no', 'now', 'name', etc...])
            |    
            | -- dict_key(['page==2', 'thread for page==2'])
                                       |    
                                       |
                                       └───dict_keys(['no', 'now', 'name', etc...])
```
- *I hope you like my markdown tree thingy.*

So our problem statement is the following:

**How do we access all the pages and all the threads with one get request?**

**By using [enumerate()](https://www.python.org/dev/peps/pep-0279/) and a [generator](https://docs.python.org/3/reference/expressions.html#yield-expressions) of course!!**

```python
import requests

r = requests.get('https://a.4cdn.org/pol/catalog.json')
r = r.json()

def gen_chan():
    for idx, page in enumerate(r):
        for thread in r[idx]['threads']:
            yield thread
```
Generators are an entire discussion unto themselves, but as you can see using the yield statement, instead of return, gives us a  [generator function](https://wiki.python.org/moin/Generators).

Without getting too off topic, this is a good opportunity to talk about a common python 'anti-pattern'.

There are two ways to do this, both of them achieve the same objective.

**Method #1(the not so great way):**
```python
import requests

r = requests.get('https://a.4cdn.org/pol/catalog.json')
r = r.json()

for pages in range(0, len(r)):
    for threads in r[pages]['threads']:
        print(threads)

#Out:
0
1
2
3
4
5
6
7
8
9
10
```
By using a nested for loop, our first loop indexes the length of the **['pages']** key.

We can then use that index as the key to our pages dictionary and access all the posts on each page.

In this case using [range()](https://www.python.org/dev/peps/pep-0281/) works just fine, but a better way would be to use [enumerate()](https://www.python.org/dev/peps/pep-0279/), inside of a generator function.

**Method #2(the more right way):**
```python
import requests

r = requests.get('https://a.4cdn.org/pol/catalog.json')
r = r.json()

def gen_chan():
    for idx, page in enumerate(r):
        print(idx)
        #for thread in r[idx]['threads']:
            #yield thread

#Out:
0
1
2
3
4
5
6
7
8
9
10
```
Again, if we **print(idx)** in the first for loop we receive all the pages at the same time.

We can use that output to index each page iteratively and extract all the contents, using the second for loop to access the thread keys.

**Step 6:**
---
Now lets get the comments for each post.

They are stored in a key called **['last_replies']**.

This is tricky because not all threads have comments, and if they do, they're always being updated from the time the post is created until it's archived.

To do this I chose the [.get()](https://docs.python.org/3.7/library/stdtypes.html#mapping-types-dict) built-in dictionary method so we dont receive any [KeyErrors](https://docs.python.org/3/library/exceptions.html) in the event a key is not present, because there are no comments on the thread yet.

Fyi, this is a little helper function I made incase I want to change our extraction method later on.
```python
def get_threads(key: str, default='NaN'):
    return threads.get(key, default)
```

Here is the resulting addition to the code.

```python
import requests
from datetime import datetime as dt

r = requests.get('https://a.4cdn.org/pol/catalog.json')
r = r.json()

def gen_chan():
    for idx, page in enumerate(r):
        for thread in r[idx]['threads']:
            yield thread

def get_threads(key: str, default='NaN'):
    return threads.get(key, default)
    
for threads in gen_chan():
    no = get_threads('no')
    now = get_threads('now')
    time = get_threads('time')
    my_time = dt.today()
    com = TextMaster.strip_html(get_threads('com'))
    name = get_threads('name')
    trip = get_threads('trip')
    ids = get_threads('id')
    capcode = get_threads('capcode')
    filename = get_threads('filename') + get_threads('ext')
    resto = get_threads('resto')
    semantic_url = get_threads('semantic_url')
    replies = get_threads('replies')
    images = get_threads('images')
    url = TextMaster.extract_url(get_threads('com'))
    sent = TextMaster.textblob_sentiment(get_threads('com'))
    if 'last_replies' in threads:
        for comment in threads['last_replies']:
            com_com = TextMaster.strip_html(comment.get('com', 'NaN'))
            resto_com = comment.get('resto', 'NaN')
            now_com = comment.get('now', 'NaN')
            time_com = comment.get('time', 'NaN')
            filename_com = comment.get('filename', 'NaN') + comment.get('ext', 'NaN')
            # If your wondering what TextMaster is, check back for my follow up article.
            url_com = TextMaster.extract_url(comment.get('com'))
            sent_com = TextMaster.textblob_sentiment(comment.get('com'))
```

For this article that pretty much sums it up.

We can now build a NLP pre-processing pipeline around it and insert it into a database.

