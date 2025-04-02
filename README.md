# Setting and Changing User Agent with Python Requests

[![Bright Data Promo](https://github.com/luminati-io/LinkedIn-Scraper/raw/main/Proxies%20and%20scrapers%20GitHub%20bonus%20banner.png)](https://brightdata.com/)

This guide explains how to set and rotate the User-Agent header in Python Requests for safe and successful web scraping:

- [Why You Should Always Set the User Agent Header](#why-you-should-always-set-the-user-agent-header)
- [What Is the Default Requests Python User Agent?](#what-is-the-default-requests-python-user-agent)
- [How to Change the Python Requests User Agent](#how-to-change-the-python-requests-user-agent)
- [Implement User Agent Rotation in Requests](#implement-user-agent-rotation-in-requests)

## Why You Should Always Set the User Agent Header

The User-Agent HTTP header identifies the client software making the request. It typically includes details about the browser type, operating system, and architecture.

Here's an example of a Chrome user agent:

```
Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/125.0.0.0 Safari/537.36
```

This string contains:

* `Mozilla/5.0`: A historical prefix indicating Mozilla compatibility
* `Windows NT 10.0; Win64; x64`: Operating system, platform, and architecture
* `AppleWebKit/537.36`: Browser engine
* `KHTML, like Gecko`: Compatibility indicators
* `Chrome/125.0.0.0`: Browser name and version
* `Safari/537.36`: Safari compatibility

The user agent helps websites determine whether a request comes from a legitimate browser or potentially automated software. Anti-scraping systems often look at this header to identify and block bots, which typically use default or inconsistent user agent strings.

## What Is the Default Requests Python User Agent?

The Python Requests library sets a default User-Agent header in the format:

`python-requests/X.Y.Z`

Where X.Y.Z is your installed Requests version. You can verify this by making a test request:

```python
import requests

# make an HTTP GET request to the specified URL
response = requests.get('https://httpbin.io/user-agent')

# parse the API response as JSON and print it
print(response.json())
```

The output will look like:

```
{'user-agent': 'python-requests/2.32.3'}
```

This clearly identifies your request as coming from an automated tool rather than a browser, making it easy for websites to detect and block your scraping attempts.

## How to Change the Python Requests User Agent

### Set a Custom User Agent

Customize the User-Agent by passing it in the headers dictionary:

```python
import requests

# custom user agent header

headers = {

  'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/125.0.0.0 Safari/537.36'

}

# make an HTTP GET request to the specified URL

# setting custom headers

response = requests.get('https://httpbin.io/user-agent', headers=headers)

# parse the API response as JSON and print it

print(response.json())
```

To set a user agent for all requests in a session:

```python
import requests

# initialize an HTTP session

session = requests.Session()

# set a custom header in the session

session.headers['User-Agent'] = 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/125.0.0.0 Safari/537.36'

# perform a GET request within the HTTP session

response = session.get('https://httpbin.io/user-agent')

# print the data returned by the API

print(response.json())

# other requests with a custom user agent within the session ...
```

This will produce the same output as before.

### Unset the User Agent

Although not recommended, you might occasionally need to remove the User-Agent header. Setting it to `None` won't work as expected:

```python
import requests

# custom user agent header

headers = {

  'user-agent': None

}

# make an HTTP GET request to the specified URL

# setting custom headers

response = requests.get('https://httpbin.io/user-agent', headers=headers)

# parse the API response as JSON and print it

print(response.json())
```

This approach still results in the urllib3 default user agent:

```
{'user-agent': 'python-urllib3/2.2.1'}
```

To completely remove the header, use `urllib3.util.SKIP_HEADER`:

```python
import requests

import urllib3

# exclude the default user agent value

headers = { 

  'user-agent': urllib3.util.SKIP_HEADER 

}

# prepare the HTTP request to make

req = requests.Request('GET', 'https://httpbin.io/headers')

prepared_request = req.prepare()

# set the custom headers with no user agent

prepared_request.headers = headers

# create a requests session and perform

# the request

session = requests.Session()

response = session.send(prepared_request)

# print the returned data

print(response.json())
```

Run the above Python code, and you will receive:

```
{'headers': {'Accept-Encoding': ['identity'], 'Host': ['httpbin.io']}}
```

## Implement User Agent Rotation in Requests

Using a single user agent for multiple requests can still trigger anti-bot systems. User agent rotation makes your requests appear to come from different browsers.

Here's how to implement it:

### Step 1: Create a User Agent List

```python
user_agents = [

    "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/125.0.0.0 Safari/537.36",

    "Mozilla/5.0 (Macintosh; Intel Mac OS X 14.5; rv:126.0) Gecko/20100101 Firefox/126.0",

    "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:126.0) Gecko/20100101 Firefox/126.0"

    # other user agents...

]
```

### Step 2: Select a Random User Agent

Randomly extracts a user agent string from the array using [`random.choice()`](https://docs.python.org/3/library/random.html#random.choice):

```python
random_user_agent = random.choice(user_agents)
```

The above line requires the following import:

```python
import random
```

### Step 3: Use the Random User Agent

Define the header dictionary with the random user agent and use it in the `requests` request:

```python
headers = {

  'user-agent': random_user_agent

}

response = requests.get('https://httpbin.io/user-agent', headers=headers)

print(response.json())
```

These instructions require this import:

```python
import requests
```

### Step 4: Complete Implementation

This is the entire script code:

```python
import random

import requests

# list of user agents

user_agents = [

    "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/125.0.0.0 Safari/537.36",

    "Mozilla/5.0 (Macintosh; Intel Mac OS X 14.5; rv:126.0) Gecko/20100101 Firefox/126.0",

    "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:126.0) Gecko/20100101 Firefox/126.0"

    # other user agents...

]

# pick a random user agent from the list

random_user_agent = random.choice(user_agents)

# set the random user agent

headers = {

  'user-agent': random_user_agent

}

# perform a GET request to the specified URL

# and print the response data

response = requests.get('https://httpbin.io/user-agent', headers=headers)

print(response.json())
```

Run this script multiple times to see different user agents in action.

## Conclusion

Setting appropriate User-Agent headers is essential for successful web scraping with Python Requests. By customizing and rotating user agents, you can make your automated requests appear more natural and avoid basic anti-bot detection.

However, sophisticated anti-scraping systems may still detect your automation through other means. For more robust scraping, consider using proxy rotation alongside user agent rotation, or explore our dedicated [Web Scraper API](https://brightdata.com/products/web-scraper) that handles these complexities for you.