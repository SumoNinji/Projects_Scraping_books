# Book Price Scraper

## Overview

This project is a web scraping script that extracts book titles and prices from the "Books to Scrape" website. It utilizes Python libraries such as `requests`, `BeautifulSoup`, `pandas`, `matplotlib`, and `seaborn` to gather data, perform analysis, and visualize the results.

## Features

- Scrapes book titles and prices from the website.
- Cleans and processes price data using regular expressions.
- Displays the results in a pandas DataFrame.
- Visualizes the distribution of book prices using histograms.

## Requirements

To run this project, you need to have Python installed along with the following libraries:

- `requests`
- `beautifulsoup4`
- `pandas`
- `matplotlib`
- `seaborn`

You can install the required libraries using pip:

```bash
pip install requests beautifulsoup4 pandas matplotlib seaborn
```

## Usage

1. **Clone the Repository** (if applicable):
   ```bash
   git clone <repository-url>
   cd <repository-directory>
   ```

2. **Run the Script**:
   - Open your Python environment or a Jupyter notebook.
   - Copy and paste the provided code into your script or notebook.
   - Run the script to scrape book prices.

### Example Code

Here’s the complete code for scraping book prices:

```python
import requests
from bs4 import BeautifulSoup
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import re

def scrape_book_prices():
    # URL of the website to scrape
    url = 'http://books.toscrape.com/'
    
    try:
        # Make a request to the website
        response = requests.get(url, timeout=10)
        response.raise_for_status()  # Raise an exception for bad status codes
        
        # Parse the HTML content
        soup = BeautifulSoup(response.text, 'html.parser')
        
        # Find all book containers
        books = soup.select('.product_pod')
        
        # Lists to hold data
        titles = []
        prices = []
        
        # Loop through each book container and extract the title and price
        for book in books:
            try:
                title = book.h3.a['title']  # Get the title attribute
                price_elem = book.find('p', class_='price_color')
                
                if price_elem:
                    # Extract price using regex to remove all non-numeric characters except decimal point
                    price_str = price_elem.text
                    price = float(re.sub(r'[^\d.]', '', price_str))
                    
                    titles.append(title)
                    prices.append(price)
            except (AttributeError, ValueError) as e:
                print(f"Error processing a book: {e}")
        
        # Create a DataFrame with the extracted data
        df = pd.DataFrame({
            'Title': titles,
            'Price': prices
        })
        
        # Display the DataFrame and statistics
        print(df)
        print("\nDataset Statistics:")
        print(df.describe())
        
        # Plot the distribution of book prices
        plt.figure(figsize=(12, 6))
        sns.histplot(df['Price'], bins=20, kde=True)
        plt.title('Distribution of Book Prices')
        plt.xlabel('Price (£)')
        plt.ylabel('Frequency')
        plt.show()
        
        return df
    
    except requests.RequestException as e:
        print(f"Error fetching the website: {e}")
        return None

# Run the scraping function
scrape_book_prices()
```
