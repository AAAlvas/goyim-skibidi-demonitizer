Certainly! Below is a sample Python program that uses Beautiful Soup to scrape data from a web page and inserts the scraped data into a MariaDB database. This example assumes you're scraping product information (like name and price) from a hypothetical e-commerce site.

Please ensure you have the following packages installed:

Beautiful Soup: For web scraping. Install it via pip if you haven't already:
      pip install beautifulsoup4 requests
  

MariaDB Connector: For connecting to the MariaDB database. Install it via pip:
      pip install mariadb
  

Python Script Example

import requests
from bs4 import BeautifulSoup
import mariadb
import sys

Database connection parameters
DB_HOST = "localhost"  # Your MariaDB host
DB_USER = "your_username"  # Your MariaDB username
DB_PASSWORD = "your_password"  # Your MariaDB password
DB_NAME = "your_database"  # Your MariaDB database name

Function to connect to the database
def connect_to_db():
    try:
        conn = mariadb.connect(
            host=DB_HOST,
            user=DB_USER,
            password=DB_PASSWORD,
            database=DB_NAME
        )
        return conn
    except mariadb.Error as e:
        print(f"Error connecting to MariaDB Platform: {e}")
        sys.exit(1)

Function to create a table if it doesn't exist
def create_table(conn):
    cur = conn.cursor()
    cur.execute("""
        CREATE TABLE IF NOT EXISTS products (
            id INT AUTO_INCREMENT PRIMARY KEY,
            name VARCHAR(255) NOT NULL,
            price DECIMAL(10, 2) NOT NULL
        )
    """)
    conn.commit()

Function to scrape product data from the given URL
def scrape_product_data(url):
    response = requests.get(url)
    soup = BeautifulSoup(response.content, 'html.parser')

Example scraping logic (modify according to actual HTML structure):
    products = []
    for product_div in soup.find_all('div', class_='product'):
        name = product_div.find('h2', class_='product-title').text.strip()
        price = product_div.find('span', class_='product-price').text.strip().replace('$', '')

        products.append((name, float(price)))  # Store as tuple (name, price)

    return products

Function to insert products into the database
def insert_products(conn, products):
    cur = conn.cursor()
    for product in products:
        cur.execute("INSERT INTO products (name, price) VALUES (?, ?)", product)
    conn.commit()

def main():
URL of the website to scrape
    url = "https://example.com/products"  # Replace with the actual URL

Connect to the database
    conn = connect_to_db()

Create table if it doesn't exist
    create_table(conn)

Scrape products
    products = scrape_product_data(url)

Insert products into the database
    insert_products(conn, products)

    print(f"Inserted {len(products)} products into the database.")

Close the connection
    conn.close()

if name == "main":
    main()

How to Use This Code
Adjust the Database Credentials: Replace your_username, your_password, and your_database with your actual MariaDB credentials and database name.

Modify the Scraping Logic: The scraping logic in the scrape_product_data function should be adjusted according to the actual structure of the website you're scraping. The example uses div.product, h2.product-title, and span.product-price, which are placeholders. Inspect the target webpage to find the correct selectors.

Run the Script: After making the necessary adjustments, you can run the script using:
      python your_script_name.py
  

Additional Considerations
Respect robots.txt**: Always ensure you are permitted to scrape a website by checking its robots.txt file before doing so.
Error Handling**: You may want to add more robust error handling, especially around network requests and database operations.
Rate Limiting**: Consider adding time delays between requests to avoid overwhelming the server.

This will be a good starting point for scraping data, inserting it into a MariaDB database, and modifying it according to your requirements!
