# Web Scraping FAQ

## Basic Information

**Description:** This FAQ covers common questions and issues related to web scraping using Python.

**Target Audience:** Beginner to intermediate Python developers interested in data extraction.

**Focus Areas:** Getting started, core functionality, troubleshooting, and advanced scraping techniques.

---

## 1. Getting Started

**Q1: What is web scraping?**  
**A:** Web scraping is the process of extracting data from websites automatically using scripts or tools instead of manual copying.

**Q2: Do I need programming knowledge?**  
**A:** Yes, basic Python knowledge (loops, functions, variables) is recommended.

**Q3: Which tools are commonly used?**  
**A:**
- requests – for HTTP requests  
- BeautifulSoup – for parsing HTML  
- pandas – for storing data  
- Selenium – for dynamic websites

**Q4: Is web scraping legal?**  
**A:** It depends on the website. Always check Terms of Service and robots.txt.

---

## 2. Features and Functionality

**Q5: How do I extract data from a webpage?**  
```python
import requests
from bs4 import BeautifulSoup

response = requests.get("http://quotes.toscrape.com")
soup = BeautifulSoup(response.text, "html.parser")
quotes = soup.find_all("span", class_="text")
```

**Q6: How do I scrape multiple pages?**  
```python
for page in range(1, 6):
    url = f"http://quotes.toscrape.com/page/{page}/"
```

**Q7: How do I handle JavaScript content?**  
**A:** Use Selenium or Playwright, or find the API endpoint.

**Q8: How do I store scraped data?**  
```python
import pandas as pd
pd.DataFrame(data).to_csv("output.csv", index=False)
```

---

## 3. Troubleshooting Common Issues

**Q9: Why am I getting a 403 error?**  
**A:** Add headers to mimic a browser:
```python
headers = {"User-Agent": "Mozilla/5.0"}
requests.get(url, headers=headers)
```

**Q10: Why is my data empty?**  
**A:** Check if your HTML selectors are correct.

**Q11: Why is my CSV empty?**  
**A:** Ensure your data list is populated before saving.

**Q12: How do I avoid getting blocked?**  
**A:** Add delays using time.sleep() and limit requests.

---

## 4. Advanced Topics

**Q13: Can I scrape login-protected sites?**  
**A:** Yes, using sessions or browser automation tools.

**Q14: How do I scrape large datasets?**  
**A:** Use batching, rate limiting, and proper error handling.

**Q15: Where can I practice?**  
- http://quotes.toscrape.com  
- http://books.toscrape.com  

---

## 5. Common Mistakes

- Not handling request errors  
- Using incorrect selectors  
- Ignoring website rules  
- Sending too many requests too quickly  

---

## 6. Troubleshooting Table

| Problem | Solution |
|--------|---------|
| 403 Error | Add headers or slow requests |
| No Data | Check selectors |
| Empty CSV | Ensure data is collected |
| Missing JS Data | Use Selenium |
