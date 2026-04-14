# 🕸️ Web Scraping in Python: Summary Notes

## 1. Core Libraries
* **`urllib.request`**: The **Messenger**. Handles the HTTP connection to fetch raw HTML from a server.
* **`BeautifulSoup`**: The **Parser**. Converts messy HTML into a structured "Tree" that Python can search.
* **`json`**: The **Vault**. Exports Python dictionaries/lists into a universal data format.

---

## 2. The Scraping Pipeline
The standard workflow follows four main steps:
1.  **Request**: Send a request with a `User-Agent` to mimic a browser.
2.  **Parse**: Load the raw HTML into a BeautifulSoup "soup" object.
3.  **Extract**: Use search methods to find specific tags, IDs, or Classes.
4.  **Save**: Dump the collected data into a JSON file.

---

## 3. Essential Methods & Logic

### Finding Elements
| Method | Description |
| :--- | :--- |
| `soup.find()` | Returns the **first** occurrence of a tag. |
| `soup.find_all()` | Returns a **list** of all occurrences. |
| `.get_text()` | Extracts all text inside a tag (safer than `.string`). |
| `.prettify()` | Formats HTML with indents so it's human-readable. |

### Identifying Content
* **ID**: Unique identifier (use `id='name'`).
* **Class**: Group identifier (use `class_='name'`). *Note the underscore in BeautifulSoup!*

### File Handling Modes
* `'r'`: **Read** (Default). Opens file to look at data.
* `'w'`: **Write**. Creates/Overwrites a file from scratch.
* `'a'`: **Append**. Adds data to the end of an existing file.

---

## 4. Key Code Snippet
```python
import urllib.request as urllib2
from bs4 import BeautifulSoup
import json

# 1. Fetch
req = urllib2.Request(url, headers={'User-Agent': 'Mozilla/5.0'})
html = urllib2.urlopen(req).read()

# 2. Parse
soup = BeautifulSoup(html, 'html.parser')

# 3. Extract
data = [p.get_text(strip=True) for p in soup.find_all('p')]

# 4. Save
with open("data.json", "w") as f:
    json.dump({"paragraphs": data}, f, indent=4)