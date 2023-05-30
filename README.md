# Book Scraper

This repository contains a Scrapy spider for scraping book information from the website [books.toscrape.com](https://books.toscrape.com/). The spider collects book titles, prices, URLs, and other details from the website.

## Setup

Follow the instructions below to set up the project and run the spider.

1. Install dependecies mentioned in `requirements.txt`
```
pip install -r requirements.txt
```
2. Navigate to the spider directory:
```
 cd part/bookscraper/bookscraper/spiders 
```

3. Check virtual env:
```
virtualenv --version 
```

 4. Create a virtual environment:
```
python3 -m venv env
```

5. Activate the virtual environment:
```
source env/bin/activate
```
6. Install the required dependencies:
```
pip install scrapy ipython
```
7. In terminal  `scrapy shell` 
8. `fetch('https://books.toscrape.com/')`

## Usage
To run the spider and collect book data, use the following command:
```
scrapy crawl bookspider
```
By default, the spider will output the scraped data to the console. If you want to save the data to a file, you can use the `-o` option followed by the desired output format. For example, to save the data to a CSV file, you can use the following command:
```
scrapy crawl bookspider -o bookdata.csv
```
## Spider Details

The spider, named `bookspider`, is defined in the file `bookspider.py` within the `spiders` directory. It starts by visiting the main page of [books.toscrape.com](https://books.toscrape.com/) and collects book information from each page.

The spider collects the following information for each book:

-   Name
-   Price
-   URL

It also supports pagination and automatically follows the "Next" button to scrape data from multiple pages.

## Project Structure

The project structure is as follows:
```
bookscraper/
├── bookscraper/
│   ├── __init__.py
│   ├── items.py
│   ├── middlewares.py
│   ├── pipelines.py
│   ├── settings.py
│   └── spiders/
│       ├── __init__.py
│       └── bookspider.py
├── scrapy.cfg
└── README.md
```
The `items.py` file defines the data model for the scraped items, and the `pipelines.py` file contains pipelines for processing and storing the scraped data.

## Ignored Files

The following files and directories are ignored in the repository:

.gitignore 
```
`__pycache__/
.Python
env/
build/` 
```
These files and directories are typically generated during the development process and are not necessary for the functionality of the project.

Please make sure to add the necessary configurations and modify the spider according to your specific requirements.

Happy scraping!