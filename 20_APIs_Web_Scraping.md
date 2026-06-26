# 20. APIs & Web Scraping

## Theoretical Questions

### Q1: What is an API and why do data analysts need to understand them?
**A:** API (Application Programming Interface) is a set of rules that allows different software applications to communicate. Data analysts need APIs because: much of the world's data lives behind APIs (social media, financial data, weather, government data), ETL pipelines often extract from APIs, and understanding APIs enables self-service data collection without waiting for engineering teams.

### Q2: What is REST and what are its key principles?
**A:** REST (Representational State Transfer) is an architectural style for designing networked applications. Key principles:
- **Stateless:** Each request contains all information needed; server doesn't store client state between requests
- **Client-Server:** Separation of concerns — client handles UI, server handles data
- **Uniform Interface:** Standard HTTP methods (GET, POST, PUT, DELETE), standard resource URLs
- **Cacheable:** Responses can be cached to improve performance
- **Layered System:** Client doesn't need to know if it's talking directly to server or through intermediaries

### Q3: What are the common HTTP methods and when do you use each?
**A:**
- **GET:** Retrieve data from server. Safe and idempotent. Used for reading data.
- **POST:** Create new resource. Not idempotent (calling twice creates two resources). Used for submitting data.
- **PUT:** Update/replace entire resource. Idempotent. Used for full updates.
- **PATCH:** Partial update to a resource. Used when you only want to change specific fields.
- **DELETE:** Remove a resource. Idempotent. Used for deletion.

### Q4: What are HTTP status codes and what do the common ones mean?
**A:**
- **2xx Success:** 200 OK, 201 Created, 204 No Content
- **3xx Redirection:** 301 Moved Permanently, 304 Not Modified
- **4xx Client Error:** 400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found, 429 Too Many Requests
- **5xx Server Error:** 500 Internal Server Error, 502 Bad Gateway, 503 Service Unavailable
**Data analyst note:** 429 means you're hitting rate limits — slow down. 401 means your API key is wrong or expired.

### Q5: What is JSON and why is it the standard for APIs?
**A:** JSON (JavaScript Object Notation) is a lightweight data interchange format. Standard because: human-readable, language-independent, easy to parse in any programming language, supports nested structures (objects and arrays), and is less verbose than XML. Structure: key-value pairs `{"name": "value"}`, arrays `[1, 2, 3]`, and nested objects.

### Q6: What is authentication in APIs and what are the common methods?
**A:** Verifying who you are before allowing access. Common methods:
- **API Key:** Simple token passed in header or URL. Easy but less secure.
- **OAuth 2.0:** Token-based authorization where user grants limited access without sharing password. Used by Google, Twitter, etc.
- **Basic Auth:** Username:password encoded in Base64. Simple but should always use HTTPS.
- **Bearer Token:** JWT or opaque token in Authorization header. Common for modern APIs.
- **HMAC:** Cryptographic signature for request integrity. Used by AWS.

### Q7: What is rate limiting and how do you handle it?
**A:** APIs restrict how many requests you can make in a time period (e.g., 100 requests/minute). Handling:
- Read API documentation for limits
- Implement exponential backoff (wait longer between retries after 429 errors)
- Use caching to avoid redundant requests
- Batch requests where possible
- Respect Retry-After headers
- Consider paid tiers for higher limits
- Use multiple API keys (if allowed) for parallel processing

### Q8: What is pagination in APIs and what are the common patterns?
**A:** Breaking large result sets into smaller chunks. Patterns:
- **Offset/Limit:** `?offset=100&limit=50` — skip first 100, return next 50. Simple but slow for large offsets.
- **Cursor/Token:** `?cursor=abc123` — server provides a token for the next page. More efficient for large datasets.
- **Page Number:** `?page=3&per_page=50` — intuitive but same offset issues.
- **Time-based:** `?since=2024-01-01` — fetch records since a timestamp. Good for incremental sync.

### Q9: What is web scraping and when is it appropriate?
**A:** Web scraping is extracting data from websites by parsing HTML. Appropriate when:
- No API is available for the data you need
- The data is publicly available and not behind a login
- You comply with the site's robots.txt and terms of service
- You don't overload the site's servers (be respectful)
**Not appropriate when:** data is private, behind authentication, or prohibited by terms of service. Always check robots.txt and consider legal/ethical implications.

### Q10: What is the difference between an API and web scraping?
**A:**
- **API:** Structured, intended for programmatic access, stable data format, rate-limited but expected, and legal/ethical when following terms.
- **Web Scraping:** Unstructured HTML parsing, fragile (breaks when site redesigns), may violate terms of service, and requires parsing HTML/CSS. Use APIs when available; scraping is a fallback.

### Q11: What is BeautifulSoup and when do you use it?
**A:** BeautifulSoup is a Python library for parsing HTML and XML. Use it when: you need to extract specific elements from web pages (tables, divs, spans), the HTML structure is relatively stable, and you need to navigate the DOM tree. It creates a parse tree from HTML that you can search and filter. Best for: simple scraping tasks where you know the page structure.

### Q12: What is the `requests` library in Python?
**A:** The standard Python library for making HTTP requests. Simple, human-friendly API: `requests.get(url)`, `requests.post(url, json=data)`, `requests.put(url, headers=headers)`. Handles: connection pooling, SSL, cookies, authentication, and JSON parsing automatically. Essential for any Python developer working with APIs.

### Q13: What is Selenium and when do you need it for data collection?
**A:** Selenium is a browser automation tool. Needed when:
- The website uses JavaScript to load data dynamically (API calls hidden behind JS)
- You need to interact with the page (click buttons, fill forms, scroll)
- The site has anti-scraping measures that detect headless requests
Trade-off: much slower than requests/BeautifulSoup because it launches a real browser. Use as last resort.

### Q14: What is an API endpoint and how do you read API documentation?
**A:** An endpoint is a specific URL where an API can be accessed. Reading docs:
1. **Base URL:** The root address (e.g., `https://api.example.com/v1/`)
2. **Endpoints:** Available paths (e.g., `/users`, `/orders/{id}`)
3. **Methods:** Which HTTP methods each endpoint supports
4. **Parameters:** Required vs. optional query params, headers, and body
5. **Authentication:** How to authenticate (API key location, OAuth flow)
6. **Rate Limits:** How many requests allowed
7. **Response Format:** What JSON structure to expect
8. **Error Codes:** What each status code means for this API

### Q15: What is a webhook and how does it differ from polling an API?
**A:**
- **Polling:** Your script repeatedly asks the API "any new data?" (e.g., every 5 minutes). Wasteful, delayed, but simple.
- **Webhook:** The API pushes data to your URL when events happen. Efficient, real-time, but requires you to run a server to receive the push.
For data analysts: polling is easier to implement; webhooks are better for real-time needs but require infrastructure.

### Q16: What is GraphQL and how does it compare to REST?
**A:** GraphQL is a query language for APIs where clients request exactly the fields they need. Compared to REST:
- **REST:** Multiple endpoints, fixed response structure, may over-fetch or under-fetch data
- **GraphQL:** Single endpoint, client specifies fields, no over-fetching, strongly typed schema
**Use GraphQL when:** the API supports it and you need precise data control. Most APIs are still REST.

### Q17: What is a POST request body and what formats can it use?
**A:** The data sent to the server in a POST request. Formats:
- **JSON:** `{"key": "value"}` — most common for modern APIs, Content-Type: application/json
- **Form Data:** `key=value&key2=value2` — for HTML forms, Content-Type: application/x-www-form-urlencoded
- **Multipart:** For file uploads, Content-Type: multipart/form-data
- **XML:** Legacy SOAP APIs, Content-Type: application/xml

### Q18: What is an API response and how do you parse it?
**A:** The data returned by the server. Structure:
- **Status Code:** HTTP code indicating success/failure
- **Headers:** Metadata (content-type, rate limit info, caching)
- **Body:** The actual data (usually JSON)
Parsing in Python: `response.json()` converts JSON body to Python dict/list. Then navigate with standard Python: `data['results'][0]['name']`.

### Q19: What are query parameters vs. path parameters in APIs?
**A:**
- **Path Parameters:** Part of the URL path, identify a specific resource. Example: `/users/123` — 123 is a path parameter (user ID)
- **Query Parameters:** Appended to URL with `?`, filter/sort/paginate. Example: `/users?role=admin&sort=name`
Path params are required for the endpoint; query params are optional modifiers.

### Q20: What is API versioning and why does it matter?
**A:** APIs evolve over time (new fields, changed behavior). Versioning ensures existing code doesn't break. Common patterns:
- **URL:** `/v1/users`, `/v2/users`
- **Header:** `Accept: application/vnd.api+json;version=2`
- **Query Param:** `?api-version=2`
**Matter for data analysts:** Your ETL pipeline may break if the API changes without warning. Pin to a specific version and monitor for deprecation notices.

### Q21: What is a User-Agent header and why is it important for scraping?
**A:** Identifies the client making the request (browser, script, bot). Important because: many websites block requests with no User-Agent or with obvious bot User-Agents. For scraping: set a realistic User-Agent like `Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36`. But don't impersonate maliciously — some sites block scrapers regardless.

### Q22: What is robots.txt and should you follow it?
**A:** A file at the root of a website (`example.com/robots.txt`) that tells web crawlers which pages they can or cannot access. Format: `User-agent: * Disallow: /private/`. **Ethical and legal best practice:** Follow robots.txt. It indicates the site owner's intent. However, robots.txt is not legally binding — terms of service are. Respectful scraping means: follow robots.txt, don't overload servers, and don't scrape private data.

### Q23: What is the difference between synchronous and asynchronous API requests?
**A:**
- **Synchronous:** Your code waits for the response before continuing. Simple to write but slow for multiple requests (they run sequentially). `requests.get()` is synchronous.
- **Asynchronous:** Your code sends requests and continues, handling responses as they arrive. Faster for I/O-bound tasks. In Python: `aiohttp`, `asyncio`, or `httpx`. Essential for: fetching data from 100 APIs concurrently.

### Q24: What is an API wrapper/library and why use one?
**A:** A library that abstracts the raw HTTP calls into convenient Python functions. Example: `tweepy` for Twitter API, `spotipy` for Spotify, `praw` for Reddit. Benefits: handles authentication, pagination, rate limiting, and error handling automatically. Use when available; write raw requests only when no wrapper exists.

### Q25: What is data serialization and why does it matter for APIs?
**A:** Converting data structures to a format that can be stored or transmitted (JSON, XML, CSV, Parquet). For APIs: JSON is the standard serialization format. Matters because: different languages represent data differently, and serialization provides a common interchange format. Python's `json.dumps()` serializes a dict to a JSON string; `json.loads()` deserializes back.

### Q26: What is a session in the context of API requests?
**A:** A session object (e.g., `requests.Session()`) persists certain parameters across requests: cookies, authentication headers, and connection pooling. Benefits: faster repeated requests to the same host (reuses TCP connection), automatic cookie handling, and cleaner code. Use for: scraping multiple pages of the same site, or making many API calls with the same auth.

### Q27: What is an API schema and why is it useful?
**A:** A formal definition of the API's structure. Formats:
- **OpenAPI (Swagger):** JSON/YAML describing endpoints, parameters, responses
- **JSON Schema:** Describes the structure of JSON data
Useful for: auto-generating documentation, validating requests/responses, and generating client code. Tools: Swagger UI, Postman, and code generators.

### Q28: What is the difference between public, partner, and private APIs?
**A:**
- **Public/Open:** Available to anyone, often with registration (Google Maps, weather APIs). May have usage limits.
- **Partner:** Shared with specific business partners, requires agreement. Often has higher limits and support.
- **Private/Internal:** Used within an organization, not exposed externally. Connects internal microservices.
Data analysts typically work with public APIs or internal APIs.

### Q29: What is a CSV API response and when do you encounter it?
**A:** Some APIs return CSV instead of JSON, especially for bulk data exports (government data, financial data). Handling: `response.text` or `response.content` and parse with `pandas.read_csv()` or Python's `csv` module. Set `Accept: text/csv` header if the API supports content negotiation.

### Q30: What is API testing and what tools do data analysts use?
**A:** Verifying API behavior before building pipelines. Tools:
- **Postman:** GUI for testing requests, saving collections, and automating tests
- **curl:** Command-line tool for quick tests
- **Python requests:** Scripting tests
- **HTTPie:** User-friendly command-line HTTP client
Tests should verify: correct status codes, expected JSON structure, error handling, and rate limit behavior.

## Scenario-Based Questions

### Q31: You need to extract daily sales data from a REST API that returns 1000 records per page. How do you get all data?
**A:**
```python
import requests

all_data = []
page = 1
while True:
    response = requests.get(
        'https://api.company.com/sales',
        params={'page': page, 'per_page': 1000},
        headers={'Authorization': 'Bearer TOKEN'}
    )
    data = response.json()
    all_data.extend(data['results'])

    if not data['has_next_page']:
        break
    page += 1

# Convert to DataFrame
df = pd.DataFrame(all_data)
```

### Q32: An API requires OAuth 2.0 authentication. Walk through the flow.
**A:**
1. **Register app:** Get client_id and client_secret from the API provider
2. **Get authorization code:** Redirect user to provider's auth URL with your client_id and redirect_uri
3. **Exchange code for token:** POST the code + client_secret to token endpoint
4. **Store access_token:** Use it in Authorization: Bearer <token> header for API calls
5. **Refresh token:** When access_token expires, use refresh_token to get a new one without re-authenticating the user
```python
# Python example with requests-oauthlib
from requests_oauthlib import OAuth2Session
client = OAuth2Session(client_id, redirect_uri=redirect_uri)
token = client.fetch_token(token_url, client_secret=client_secret, code=code)
response = client.get('https://api.example.com/data')
```

### Q33: You need to scrape a table from a website that has no API. What's your approach?
**A:**
```python
import requests
from bs4 import BeautifulSoup
import pandas as pd

url = 'https://example.com/data'
headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64)'}
response = requests.get(url, headers=headers)
soup = BeautifulSoup(response.content, 'html.parser')

# Find the table
table = soup.find('table', {'id': 'data-table'})
# Extract headers
headers = [th.text.strip() for th in table.find_all('th')]
# Extract rows
rows = []
for tr in table.find_all('tr')[1:]:  # Skip header
    cells = [td.text.strip() for td in tr.find_all('td')]
    rows.append(cells)

df = pd.DataFrame(rows, columns=headers)
```

### Q34: An API returns nested JSON. How do you flatten it into a DataFrame?
**A:**
```python
import pandas as pd

# Nested JSON example
json_data = {
    'users': [
        {'id': 1, 'name': 'Alice', 'address': {'city': 'NYC', 'zip': '10001'}},
        {'id': 2, 'name': 'Bob', 'address': {'city': 'LA', 'zip': '90001'}}
    ]
}

# Method 1: pandas json_normalize
df = pd.json_normalize(json_data['users'], sep='_')
# Result: id, name, address_city, address_zip

# Method 2: Manual flattening
flattened = []
for user in json_data['users']:
    flat = {
        'id': user['id'],
        'name': user['name'],
        'city': user['address']['city'],
        'zip': user['address']['zip']
    }
    flattened.append(flat)
df = pd.DataFrame(flattened)
```

### Q35: You're hitting rate limits on an API (429 errors). How do you handle this in production?
**A:**
```python
import time
import requests

def api_call_with_backoff(url, headers, max_retries=5):
    for attempt in range(max_retries):
        response = requests.get(url, headers=headers)
        if response.status_code == 200:
            return response.json()
        elif response.status_code == 429:
            wait = int(response.headers.get('Retry-After', 2 ** attempt))
            time.sleep(wait)
        else:
            response.raise_for_status()
    raise Exception("Max retries exceeded")

# Also: implement caching to avoid redundant calls
# Use a session for connection pooling
# Consider using a queue-based approach for large batch jobs
```

### Q36: You need to scrape data from 50 pages of a website. How do you do this efficiently and respectfully?
**A:**
```python
import requests
import time
from bs4 import BeautifulSoup

base_url = 'https://example.com/items?page={}'
headers = {'User-Agent': 'Mozilla/5.0 (DataAnalysisBot/1.0; contact@company.com)'}

all_items = []
for page in range(1, 51):
    response = requests.get(base_url.format(page), headers=headers)
    response.raise_for_status()
    soup = BeautifulSoup(response.content, 'html.parser')
    items = soup.find_all('div', class_='item')
    all_items.extend(items)

    # Respectful delay: 1-2 seconds between requests
    time.sleep(1.5)

    # Check robots.txt first!
    # Add random jitter to avoid pattern detection
    # Consider using a session for connection reuse
```

### Q37: An API returns data in a format that's not JSON (XML or HTML). How do you parse it?
**A:**
```python
# XML parsing
import xml.etree.ElementTree as ET
response = requests.get('https://api.example.com/data.xml')
root = ET.fromstring(response.content)
for item in root.findall('item'):
    name = item.find('name').text
    value = item.find('value').text

# Or use xmltodict library for easier parsing
import xmltodict
data = xmltodict.parse(response.content)

# HTML table parsing (if API returns HTML)
from bs4 import BeautifulSoup
soup = BeautifulSoup(response.content, 'html.parser')
table = soup.find('table')
```

### Q38: You need to extract data from a website that loads content dynamically with JavaScript. What do you do?
**A:**
1. **Check Network tab:** Open browser DevTools → Network → XHR. See if the site makes API calls that return JSON. If yes, call those APIs directly (much faster than scraping HTML).
2. **If no API:** Use Selenium or Playwright to render the JavaScript:
```python
from selenium import webdriver
from selenium.webdriver.common.by import By

driver = webdriver.Chrome()
driver.get('https://example.com')
# Wait for dynamic content
time.sleep(3)  # Or use WebDriverWait for specific element
# Extract data
elements = driver.find_elements(By.CLASS_NAME, 'data-item')
data = [el.text for el in elements]
driver.quit()
```
3. **Alternative:** Use requests-html library which has JavaScript rendering support.

### Q39: You need to authenticate to an API using an API key. Where do you store it securely?
**A:**
- **Never:** Hardcode in scripts, commit to Git, or share in messages
- **Environment variables:** `os.environ['API_KEY']` — accessible but not in code
- **.env files:** Use `python-dotenv` to load from `.env` file (add to .gitignore!)
- **Secret managers:** AWS Secrets Manager, Azure Key Vault, HashiCorp Vault
- **Config files:** Encrypted or with restricted permissions
```python
from dotenv import load_dotenv
import os
load_dotenv()
api_key = os.getenv('API_KEY')
```

### Q40: You need to combine data from 3 different APIs with different response formats. How do you standardize them?
**A:**
```python
import pandas as pd

# Fetch from all APIs
data_a = fetch_api_a()  # Returns JSON with nested structure
data_b = fetch_api_b()  # Returns flat JSON
data_c = fetch_api_c()  # Returns CSV-like list

# Standardize to common schema
standardized = []

# API A: flatten nested JSON
for record in data_a['items']:
    standardized.append({
        'id': record['id'],
        'name': record['details']['name'],
        'value': record['metrics']['total'],
        'source': 'API_A'
    })

# API B: direct mapping
for record in data_b:
    standardized.append({
        'id': record['user_id'],
        'name': record['user_name'],
        'value': record['amount'],
        'source': 'API_B'
    })

# API C: remap columns
for record in data_c:
    standardized.append({
        'id': record[0],
        'name': record[1],
        'value': record[2],
        'source': 'API_C'
    })

df = pd.DataFrame(standardized)
```

### Q41: An API you depend on is deprecated and will shut down in 3 months. What's your migration plan?
**A:**
1. **Assess impact:** Which pipelines, dashboards, and reports use this API?
2. **Find alternative:** Is there a v2 API? A competitor API? Can you scrape the data?
3. **Parallel run:** Run both old and new data sources for 4-6 weeks to compare outputs
4. **Data validation:** Verify the new source produces equivalent or better data quality
5. **Update pipelines:** Modify ETL code, test thoroughly in staging
6. **Communication:** Notify stakeholders of any data changes or gaps
7. **Cutover:** Switch to new source, monitor for 2 weeks
8. **Documentation:** Update data dictionaries and lineage docs
9. **Contingency:** If new source fails, have a fallback plan before the 3-month deadline
