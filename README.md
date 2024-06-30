pip install requests beautifulsoup4
import requests
from bs4 import BeautifulSoup
import pandas as pd

# URL of the Election Commission of India results page
url = 'https://results.eci.gov.in/'

# Send a GET request to the page
response = requests.get(url)

# Check if the request was successful
if response.status_code == 200:
    # Parse the content of the request with BeautifulSoup
    soup = BeautifulSoup(response.content, 'html.parser')

    # Find the table containing the election results 
    # The original code assumed a 'table' class which may not exist
    # Let's try finding any table element first to see what's available
    results_table = soup.find('table') 

    if results_table: # Check if a table was found
        # Extract the headers
        headers = [header.text.strip() for header in results_table.find_all('th')]

        # Extract the rows
        rows = []
        for row in results_table.find_all('tr')[1:]:
            cells = [cell.text.strip() for cell in row.find_all('td')]
            if cells:
                rows.append(cells)

        # Create a DataFrame
        df = pd.DataFrame(rows, columns=headers)
        print(df.head())
    else:
        print("No table element found on the page. You may need to inspect the page's HTML to find the correct selector.")
else:
    print('Failed to retrieve the page.') 
# Ensure that the 'Votes' column is numeric
pd.DataFrame['Votes'] = pd.to_numeric(df['Votes'], errors='coerce')

# Summary of total votes by party
total_votes_by_party = df.groupby('Party')['Votes'].sum().sort_values(ascending=False)
print(total_votes_by_party)

# Winning candidates
winning_candidates = df[df['Status'] == 'Won']
print(winning_candidates)

# Analysis of vote share
vote_share = df.groupby('Party')['Votes'].sum() / df['Votes'].sum() * 100
print(vote_share)
import matplotlib.pyplot as plt
import seaborn as sns

# Plot total votes by party
plt.figure(figsize=(10, 6))
sns.barplot(x=total_votes_by_party.index, y=total_votes_by_party.values)
plt.xticks(rotation=90)
plt.title('Total Votes by Party')
plt.xlabel('Party')
plt.ylabel('Total Votes')
plt.show()

# Plot vote share by party
plt.figure(figsize=(10, 6))
sns.barplot(x=vote_share.index, y=vote_share.values)
plt.xticks(rotation=90)
plt.title('Vote Share by Party')
plt.xlabel('Party')
plt.ylabel('Vote Share (%)')
plt.show()
