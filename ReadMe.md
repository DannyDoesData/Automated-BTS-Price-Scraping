# 🌐Automated BTC Price Scraping From CoinMarketCap🌐

### Project goal 🎯

To write a script that will scrape BTC price in real-time from Coinmarketcap



### Tools Used 🧰
1. **Python (Libraries: Pandas, BeautifulSoup, Selenium)**
2. **Visual Studio Code**
3. **Git & GitHub**


### The process 

**First scraping a single row using BeautifulSoup and Pandas**

Initializing libraries 

```python 
from bs4 import BeautifulSoup
import requests
import time
import datetime 
import csv
import pandas as pd
import datetime
```

Cooking soup :)

```python
URL = 'https://coinmarketcap.com/currencies/bitcoin/'

headers = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/127.0.0.0 Safari/537.36"}

page = requests.get(URL, headers=headers)

soup1 = BeautifulSoup (page.content, "html.parser")

soup2 = BeautifulSoup (soup1.prettify(), 'html.parser')


coin = soup2.find(class_='sc-65e7f566-0 lsTl').get_text().strip()
coin = coin.replace('\n', '').replace(' ', '')[:7]

price = soup2.find(class_='sc-65e7f566-0 clvjgF base-text').get_text().strip()[1:]

ticker = soup2.find(class_='sc-65e7f566-0 czZVlm base-text',).get_text().strip()
```

Defining current time separately to avoid conflict 

```python
from datetime import datetime
timenow = datetime.now().strftime('%H:%M:%S')
```

Writing single price entry into CSV

```python
import datetime

today = datetime.date.today()
headers = ['Ticker','Asset','Price','Time','Date' ]
data = [ticker, coin, price, timenow, today]

with open('BitCoinAutomatedScraping_soup.csv', 'w', newline='', encoding='UTF8') as f : 
    writer = csv.writer(f)
    writer.writerow(headers)
    writer.writerow(data) 
```

```python
df = pd.read_csv(r'C:\Users\user\Desktop\VS workspaces\Automated Data Scraping\BitCoinAutomatedScraping_soup.csv')

print(df)
```

Output:   
Ticker    Asset      Price      Time        Date

   BTC  Bitcoin  59,484.61  23:23:53  2024-08-18

**✅ Success ✅**

**Now automating script for 30 iterations:** 

```python
def check_price():
    URL = 'https://coinmarketcap.com/currencies/bitcoin/'

    headers = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/127.0.0.0 Safari/537.36"}

    page = requests.get(URL, headers=headers)

    soup1 = BeautifulSoup (page.content, "html.parser")

    soup2 = BeautifulSoup (soup1.prettify(), 'html.parser')


    coin = soup2.find(class_='sc-65e7f566-0 lsTl').get_text().strip()
    coin = coin.replace('\n', '').replace(' ', '')[:7]

  
    price_element = soup2.find('div', {'sc-65e7f566-0 DDohe flexStart alignBaseline'}).get_text().strip()
    price_element = price_element.replace('\n', '').replace(' ', '')[1:10]
    price=price_element

    ticker = soup2.find(class_='sc-65e7f566-0 czZVlm base-text').get_text().strip()

    from datetime import datetime
    timenow = datetime.now().strftime('%H:%M:%S')

    import datetime
    today = datetime.date.today()
    

    data = [ticker, coin, price, timenow, today]

    with open('BitCoinAutomatedScraping_soup.csv', 'a+', newline='', encoding='UTF8') as f : 
        writer = csv.writer(f)
        writer.writerow(data) 

# Check for 30 iterations
i=0
while i<30:  
    check_price()
    time.sleep(1)
    i=i+1
df = pd.read_csv(r'C:\Users\user\Desktop\VS workspaces\Automated Data Scraping\BitCoinAutomatedScraping_soup.csv')
print(df)
```
**❌Fail❌**

**Output [Here](/BitCoinAutomatedScraping_soup.csv)**

❌Unfortunately, the BeautifulSoup library is not suited for retrieving frequently changing data❌


## Switching to Selenium library for automated scraping

Initializing libraries 

```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.chrome.options import Options
import time
import csv
from datetime import datetime
import pandas as pd
```

Automated scraping using Selenium library (30 iterations)

```python
#Chrome set up
chrome_options = Options()
chrome_options.add_argument("--headless")  

#WebDriver
driver = webdriver.Chrome()

file_name = 'BitCoinAutomatedScraping_selenium.csv'


with open(file_name, 'w', newline='', encoding='UTF8') as f:
    writer = csv.writer(f)
    writer.writerow(['Ticker', 'Asset', 'Price', 'Time', 'Date'])  # Write headers

def check_btc():
    URL = 'https://coinmarketcap.com/currencies/bitcoin/'

    driver.get(URL)
    
    time.sleep(2)

    
    price_element = driver.find_element(By.XPATH, "//span[@data-test='text-cdp-price-display']")
    price = price_element.text.strip()[1:]  # Strip the currency symbol

    coin = "Bitcoin"
    ticker = "BTC"

    timenow = datetime.now().strftime('%H:%M:%S')
    today = datetime.now().date()

    data = [ticker, coin, price, timenow, today]

    # Append the data to the CSV file
    with open(file_name, 'a+', newline='', encoding='UTF8') as f:
        writer = csv.writer(f)
        writer.writerow(data)



#Check for 30 iterations
i=0
while i<30:  
    check_btc()
    time.sleep(1)
driver.quit()  
```

**✅ Success ✅**

**Output [Here](/BitCoinAutomatedScraping_selenium.csv)**

**Selenium library proved to be effective in scraping dynamic data and will be to go choice in future endeavors**
