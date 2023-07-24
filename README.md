# amazon-Scrapper
# What is web scraping?
Web scraping is the process of obtaining data from a website. A web scraper loads the HTML code of an entire page or specific parts of a page as specified by the developer. 

# Requirements for how to scrape Amazon with Python
To scrape Amazon with Python, you need to know how to create a Python environment and code in Python. Your project will require the following Python libraries:

Selenium: This tool automates the WebDriver browser by enabling you to crawl a particular URL and gather its data in scale.
BeautifulSoup: This is a Python-based library that developers use to parse HTML and XML pages. Alot of time is required to parse and scrape a page. A parser enables you to quickly and easily search, navigate and modify data by automatically converting the document to Unicode.
WebDriver: The WebDriver protocol automates browser processes. Selenium API automates the WebDriver. These browsers are Firefox, Chrome, and Microsoft Edge.
How to scrape the Amazon website
Let’s get started on the step-by-step process of scraping the amazon.com website.

## Step 1:Getting started
Create a .py file or .pynb with Jupyter. Install BeautifulSoup and Selenium on the terminal.

## Step2: Importing necessary tools
After installation, you need to import CSV, BeautifulSoup from BS4, and WebDriver from Selenium, as shown below;


import csv
from bs4 import BeautifulSoup
from selenium import webdriver


## Step 3: Generate a URL from the search term
Visit amazon.com and type a search term on the search bar. In this case, we have searched for headphones and obtained the URL below.

https://www.amazon.com/s?k=headphones&crid=3ORS3HPQDQZ22&sprefix=head%2Caps%2C528&ref=nb_sb_ss_ts-doa-p_1_4

Note that headphones appear on the template URL as highlighted. To generate any URLs from any search term you type, you have to remove the search term on the URL above and replace it with curly brackets, as shown below.

 

def get_url(search_term):
template = ‘https://www.amazon.com/s?k={}&crid=3ORS3HPQDQZ22&sprefix=head%2Caps%2C528&ref=nb_sb_ss_ts-doa-p_1_4’
search_term = search_term.replace(‘ ‘, ‘+’)

#add term query
url = template.format(search_term)

#add page query placeholder
url += ‘&page={}’

return url

The code snippet above enables you to scrape the search term query and the page query based on the template URL.

 

## Step 4: Extract and return data from a single record 
You must visit the ‘headphones’ URL and inspect a single record to obtain its description, URL, rating, price, and review rank. 

Extracting these data can cause several errors. To handle these errors, you must add a try, except AttributeError loop, as shown below.

 

def extract_record(item):

# description and url
atag = item.h2.a
description = atag.text.strip()
url = ‘https://www.amazon.com’ + atag.get(‘href’)

# price
try:
price_parent = item.find(‘span’, ‘a-price’)
price = price_parent.find(‘span’, ‘a-offscreen’).text
except AttributeError:
return
#rank and rating
try:
rating = item.i.text
review_count = item.find(‘span’, {‘class’: ‘a-size-base s-underline-text’}).text
except AttributeError:
rating = ”
review_count = ”
result = (description, price, rating, review_count, url)

return result

## Step 5: Run the main program routine
At this point, you are ready to run the main program routine. Create a function (main) that takes the parameter (search_term), and then start the WebDriver, in this case, the ChromeDriver. 

 

After obtaining the single record URL based on the search term, you need to obtain all records within the page. Using BeautifulSoup, you can parse the records in all the pages you are scraping. Most pages on Amazon go up to 20, which is the maximum number of pages we are scraping.

 

After extracting the record data, you can append it and close the driver, as shown below.


def main(search_term):
"""run main program routine"""

#start the WebDriver
driver = webdriver.Chrome()

records = []
url = get_url(search_term)

for page in range(1, 21):
driver.get(url.format(page))
soup = BeautifulSoup(driver.page_source, ‘html.parser’)
results = soup.find_all(‘div’, {‘data-component-type’: ‘s-search-result’})

for item in results:
record = extract_record(item)
if record:
records.append(record)

driver.close()

 

## Step 6: Save the data in a CSV file
The data has to be saved on a CSV file. In this case, results.csv.


#save data in csv file
with open(‘results.csv’, ‘w’, newline=”, encoding=’utf-8′) as csvfile:
writer = csv.writer(csvfile)
writer.writerow({‘Description’, ‘Price’, ‘Rating’, ‘ReviewCount’, ‘Url’})
writer.writerows(records)


 

## Step 7: Input your search term
You can now input your search term and fire up your scraper.

main(‘Headphones’)

