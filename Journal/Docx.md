# Ignore the file

```
cd part/bookscraper/bookscraper/spiders
virtualenv --version
python3 -m venv env
source env/bin/activate
(env) gitpod /workspace/Scrapy/part/bookscraper/bookscraper/spiders (main) $ pip install ipython
terminal > scrapy shell
fetch('https://books.toscrape.com/')
```

-- Main cmd 
`(env) gitpod /workspace/Scrapy/part/bookscraper/bookscraper/spiders (main) $ scrapy crawl bookspider`

`scrapy crawl bookspider -O bookdata.csv # adds data to csv file, writes&overwrites the data`
`scrapy crawl bookspider -o bookdata.csv # appends data from stop `

----
```
python3 -m pip install --user virtualenv
python3 -m venv env
source env/bin/activate
deactivate
```
----

```
pip install scrapy
scrapy startproject bookscraper
books.toscrape.com
scrapy genspider bookspider books.toscrape.com # to create a spider
(env) gitpod /workspace/Scrapy/part/bookscraper/bookscraper/spiders (main) $ pip install ipython
go to scrapy.cfg add in settings "shell = ipython"
terminal > scrapy shell
fetch('https://books.toscrape.com/')
response
response.css('article.product_pod').get()
books  = response.css('article.product_pod') #save all the responses to books
len(books) # length of books : no of books
book.css('h3 a::text').get() #to get h3 and text
book.css('.product_price .price_color::Text').get() 
book.css('h3 a').attrib['href'] #to get link to the book
```
`scrapy crawl bookspider #cmd to run below bookspider`

```
import scrapy

class BookspiderSpider(scrapy.Spider):
    name = "bookspider"
    allowed_domains = ["books.toscrape.com"]
    start_urls = ["https://books.toscrape.com"]

    def parse(self, response):
        books = response.css('article.product_pod')

        for book in books:
            yield{
                'name' : book.css('h3 a::text').get(),
                'price': book.css('.product_price .price_color::Text').get(),
                'url' : book.css('h3 a').attrib['href']
            }
```

`response.css('li.next a::attr(href)').get() #Out[3]:'catalogue/page-2.html'`
`response.css('i.icon-ok::text').get()`

## DAy-2
### Below code goes through all the books from page 1 to last page collects name, price, url
```
import scrapy

class BookspiderSpider(scrapy.Spider):
    name = "bookspider"
    allowed_domains = ["books.toscrape.com"]
    start_urls = ["https://books.toscrape.com"]

    def parse(self, response):
        books = response.css('article.product_pod')

        for book in books:
            yield{
                'name' : book.css('h3 a::text').get(),
                'price': book.css('div.product_price .price_color::Text').get(),
                'url' : book.css('h3 a').attrib['href']
            }    
        next_page = response.css('li.next a::attr(href)').get()
        if next_page is not None:
            if 'catalogue/' in next_page:
                next_page_url =  'https://books.toscrape.com/' + next_page
            else:
                next_page_url = 'https://books.toscrape.com/catalogue/' + next_page    
            yield response.follow(next_page_url, callback = self.parse)

```
`.gitignore file:`

```
# Byte-compiled / optimized / DLL files
__pycache__/
# Distribution / packaging
.Python
env/
build/
```

### Build discover and extraction spider:
`(env) gitpod /workspace/Scrapy/part/bookscraper/bookscraper/spiders (main) $ scrapy shell`
```
fetch('https://books.toscrape.com/catalogue/a-light-in-the-attic_1000/index.html')
response.css('.product_main h1::text').get()
table_rows = response.css("table tr") # gets the no of tables used in description
len(table_rows)
table_rows[1].css('td ::text').get()
Out[9]: 'Books'
response.css('p.star-rating').attrib['class']
Out[12]: 'star-rating Three'
```
`response.css('p.price_color ::text').get()`
```
import scrapy

class BookspiderSpider(scrapy.Spider):
    name = 'bookspider'
    allowed_domains = ['books.toscrape.com']
    start_urls = ['https://books.toscrape.com/']

    def parse(self, response):
        books = response.css('article.product_pod')
        for book in books:
            relative_url = book.css('h3 a ::attr(href)').get()

            if 'catalogue/' in relative_url:
                book_url = 'https://books.toscrape.com/' + relative_url
            else:
                book_url = 'https://books.toscrape.com/catalogue/' + relative_url
            yield response.follow(book_url, callback=self.parse_book_page)

        next_page = response.css('li.next a ::attr(href)').get()
        if next_page is not None:
            if 'catalogue/' in next_page:
                next_page_url = 'https://books.toscrape.com/' + next_page
            else:
                next_page_url = 'https://books.toscrape.com/catalogue/' + next_page
            yield response.follow(next_page_url, callback=self.parse)


    def parse_book_page(self, response):

        table_rows = response.css("table tr")
        
        yield {
            'url' : response.url,
            'title' : response.css('.product_main h1::text').get(),
            'product_type': table_rows[1].css("td ::text").get(),
            'price_excl_tax': table_rows[2].css("td ::text").get(),
            'price_incl_tax': table_rows[3].css("td ::text").get(),
            'tax': table_rows[4].css("td ::text").get(),
            'availability': table_rows[5].css("td ::text").get(),
            'num_reviews': table_rows[6].css("td ::text").get(),
            'stars' : response.css("p.star-rating").attrib['class'],
            'category' : response.xpath("//ul[@class='breadcrumb']/li[@class='active']/preceding-sibling::li[1]/a/text()").get(),
            'description' : response.xpath("//div[@id='product_description']/following-sibling::p/text()").get(),
            'price': response.css('p.price_color ::text').get(),
        }
```
`scrapy crawl bookspider -O bookdata.json`
`scrapy crawl bookspider -O bookdata.csv`

-----------------------------------

#### cleaning data with item pipelines:
items.py helps in understanding/tracking items being stored in data file
pipeines.py transformation of raw data into desirable format to store in the DB

-----------------------------------

#### saving files to DB:

*problem occured install and connecting to mysql*

**settings.py** 
'''
ITEM_PIPELINES = {
    "bookscraper.pipelines.BookscraperPipeline": 300,
    'bookscraper.pipelines.SaveToMySQLPipeline': 400,
}
'''
----------------------------------------

#### Fake user-agents and headers:

ctrl+shit+i;
Headers,
user-agent, # useragentstring.com - Fine while doing small scale scraping, not a good thing for big scale.


signup to get user_agent and header from scrapops

------------------------------------------

#### API proxies:

pip install scrapy-rotating-proxies

free proxy list #genonode