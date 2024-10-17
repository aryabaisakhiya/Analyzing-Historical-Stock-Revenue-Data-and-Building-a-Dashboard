# Analyzing-Historical-Stock-Revenue-Data-and-Building-a-Dashboard

#program
```
#!/usr/bin/env python
# coding: utf-8

# In[2]:


#!pip install yfinance==0.2.38
#!pip install pandas==2.2.2
#!pip install nbformat


# In[1]:


get_ipython().system('pip install yfinance')


# In[2]:


get_ipython().system('pip install bs4')
get_ipython().system('pip install nbformat')


# In[5]:


import yfinance as yf
import pandas as pd
import requests
from bs4 import BeautifulSoup
import plotly.graph_objects as go
from plotly.subplots import make_subplots


# In[6]:


import warnings
# Ignore all warnings
warnings.filterwarnings("ignore", category=FutureWarning)


# In[7]:


def make_graph(stock_data, revenue_data, stock):
    fig = make_subplots(rows=2, cols=1, shared_xaxes=True, subplot_titles=("Historical Share Price", "Historical Revenue"), vertical_spacing = .3)
    stock_data_specific = stock_data[stock_data.Date <= '2021--06-14']
    revenue_data_specific = revenue_data[revenue_data.Date <= '2021-04-30']
    fig.add_trace(go.Scatter(x=pd.to_datetime(stock_data_specific.Date), y=stock_data_specific.Close.astype("float"), name="Share Price"), row=1, col=1)
    fig.add_trace(go.Scatter(x=pd.to_datetime(revenue_data_specific.Date), y=revenue_data_specific.Revenue.astype("float"), name="Revenue"), row=2, col=1)
    fig.update_xaxes(title_text="Date", row=1, col=1)
    fig.update_xaxes(title_text="Date", row=2, col=1)
    fig.update_yaxes(title_text="Price ($US)", row=1, col=1)
    fig.update_yaxes(title_text="Revenue ($US Millions)", row=2, col=1)
    fig.update_layout(showlegend=False,
    height=900,
    title=stock,
    xaxis_rangeslider_visible=True)
    fig.show()


# In[8]:


## Question 1: Use yfinance to Extract Stock Data

import yfinance as yf

# Define the stock ticker symbol (e.g., Tesla)
ticker = "TSLA"

# Extract historical stock data for the last 5 years
stock_data = yf.Ticker(ticker).history(period="5y")

# Reset index to get Date as a column (optional)
stock_data.reset_index(inplace=True)

# Display the first few rows of the stock data
print(stock_data.head())


# In[9]:


import yfinance as yf

# Define the Tesla stock ticker symbol
ticker = "TSLA"

# Create a ticker object for Tesla
tesla = yf.Ticker(ticker)

# Extract the maximum available historical stock data for Tesla
tesla_data = tesla.history(period="max")

# Reset the index to make 'Date' a column (optional but useful for plotting)
tesla_data.reset_index(inplace=True)

# Display the first few rows of the data
print(tesla_data.head())


# In[10]:


import yfinance as yf

# Define the Tesla stock ticker symbol
ticker = "TSLA"

# Create a ticker object for Tesla
tesla = yf.Ticker(ticker)

# Extract the maximum available historical stock data for Tesla
tesla_data = tesla.history(period="max")

# Reset the index to make 'Date' a column
tesla_data.reset_index(inplace=True)

# Display the first five rows of the DataFrame
print(tesla_data.head())

# At this point, you can take a screenshot of the results


# In[11]:


## Question 2: Use Webscraping to Extract Tesla Revenue Data
import requests

# URL of the webpage containing Tesla revenue data
url = "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/revenue.htm"

# Use requests to download the webpage
response = requests.get(url)

# Save the text of the response as a variable named html_data
html_data = response.text

# Optionally, print the first 500 characters of the HTML data to verify
print(html_data[:500])


# In[12]:


from bs4 import BeautifulSoup

# Create a BeautifulSoup object and parse the html_data
soup = BeautifulSoup(html_data, 'html.parser')  # or use 'html5lib' if you have it installed

# Optionally, print the first 500 characters of the parsed data to verify
print(soup.prettify()[:500])



# In[13]:


# Extract the table with Tesla Revenue using BeautifulSoup
table = soup.find('table')  # Find the first table in the parsed HTML
rows = table.find_all('tr')  # Get all rows in the table

# Prepare lists to hold the data
dates = []
revenues = []

# Iterate over the rows and extract data
for row in rows[1:]:  # Skip the header row
    cols = row.find_all('td')
    dates.append(cols[0].text.strip())   # Date is in the first column
    revenues.append(cols[1].text.strip())  # Revenue is in the second column

# Create a DataFrame from the extracted data
tesla_revenue = pd.DataFrame({
    'Date': dates,
    'Revenue': revenues
})

# Display the first few rows of the DataFrame
print(tesla_revenue.head())


# In[14]:


# Remove commas and dollar signs from the Revenue column and convert to float
tesla_revenue['Revenue'] = tesla_revenue['Revenue'].str.replace(',', '').str.replace('$', '').astype(float)

# Display the first few rows of the DataFrame to verify changes
print(tesla_revenue.head())


# In[15]:


# Remove rows where the Revenue column is null or contains empty strings
tesla_revenue = tesla_revenue[tesla_revenue['Revenue'].notnull() & (tesla_revenue['Revenue'] != '')]

# Display the first few rows of the DataFrame to verify changes
print(tesla_revenue.head())


# In[16]:


# Display the last 5 rows of the tesla_revenue DataFrame
print(tesla_revenue.tail())


# In[17]:


## Question 3: Use yfinance to Extract Stock Data
import yfinance as yf

# Define the GameStop stock ticker symbol
ticker = "GME"

# Create a ticker object for GameStop
gamestop = yf.Ticker(ticker)

# Optionally, you can fetch historical stock data for verification
gamestop_data = gamestop.history(period="max")

# Display the first few rows of the historical data
print(gamestop_data.head())



# In[18]:


import yfinance as yf

# Define the GameStop stock ticker symbol
ticker = "GME"

# Create a ticker object for GameStop
gamestop = yf.Ticker(ticker)

# Extract the maximum available historical stock data for GameStop
gme_data = gamestop.history(period="max")

# Reset the index to make 'Date' a column (optional)
gme_data.reset_index(inplace=True)

# Display the first few rows of the DataFrame
print(gme_data.head())


# In[19]:


import yfinance as yf

# Define the GameStop stock ticker symbol
ticker = "GME"

# Create a ticker object for GameStop
gamestop = yf.Ticker(ticker)

# Extract the maximum available historical stock data for GameStop
gme_data = gamestop.history(period="max")

# Reset the index to make 'Date' a column
gme_data.reset_index(inplace=True)

# Display the first five rows of the DataFrame
print(gme_data.head())


# In[20]:


## Question 4: Use Webscraping to Extract GME Revenue Data
import requests

# URL of the webpage containing GME revenue data
url_2 = "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/stock.html"

# Use requests to download the webpage
response_2 = requests.get(url_2)

# Save the text of the response as a variable named html_data_2
html_data_2 = response_2.text

# Optionally, print the first 500 characters of the HTML data to verify
print(html_data_2[:500])


# In[21]:


from bs4 import BeautifulSoup

# Create a BeautifulSoup object and parse the html_data_2
soup_2 = BeautifulSoup(html_data_2, 'html.parser')  # or use 'html5lib' if you prefer

# Optionally, print the first 500 characters of the parsed data to verify
print(soup_2.prettify()[:500])


# In[22]:


import requests
from bs4 import BeautifulSoup
import pandas as pd

# Step 1: Download the webpage containing GameStop revenue data
url = "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/stock.html"
response = requests.get(url)

# Step 2: Parse the HTML data using BeautifulSoup
html_data = response.text
soup = BeautifulSoup(html_data, 'html.parser')

# Step 3: Extract the table with GameStop Revenue
table = soup.find('table')  # Find the first table in the parsed HTML
rows = table.find_all('tr')  # Get all rows in the table

# Prepare lists to hold the data
dates = []
revenues = []

# Iterate over the rows and extract data
for row in rows[1:]:  # Skip the header row
    cols = row.find_all('td')
    dates.append(cols[0].text.strip())  # Date is in the first column
    revenues.append(cols[1].text.strip())  # Revenue is in the second column

# Step 4: Create a DataFrame from the extracted data
gme_revenue = pd.DataFrame({
    'Date': dates,
    'Revenue': revenues
})

# Step 5: Clean the Revenue column by removing commas and dollar signs and convert to float
gme_revenue['Revenue'] = gme_revenue['Revenue'].str.replace(',', '').str.replace('$', '').astype(float)

# Step 6: Display the first few rows of the DataFrame
print(gme_revenue.head())


# In[23]:


# Display the last 5 rows of the gme_revenue DataFrame
print(gme_revenue.tail())


# In[4]:


## Question 5: Plot Tesla Stock Graph
# Assuming tesla_data and tesla_revenue DataFrames have been defined previously

# Call the make_graph function to plot Tesla stock data
make_graph(tesla_data, tesla_revenue, "Tesla Stock and Revenue Data (up to June 2021)")


# In[24]:


## Question 6: Plot GameStop Stock Graph

# Call the make_graph function to plot GameStop stock data
make_graph(gme_data, gme_revenue, 'GameStop')


# In[38]:


## Question 6: Plot GameStop Stock Graph
print("About the Authors")
print("ARYA BAISAKHIYA")
```
