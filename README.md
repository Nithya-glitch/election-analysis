pip install requests beautifulsoup4
import requests
from bs4 import BeautifulSoup

# URL of the Election Commission of India results page
url = 'https://results.eci.gov.in'

# Send a GET request to the page
response = requests.get(url)

# Check if the request was successful
if response.status_code == 200:
    # Parse the content of the request with BeautifulSoup
    soup = BeautifulSoup(response.content, 'html.parser')

    # Find the section containing the election results
    results_section = soup.find('div', id='div1')

    # Extract and print the results
    if results_section:
        print(results_section.text)
    else:
        print('Results section not found.')

else:
    print('Failed to retrieve the page.')


import requests
from bs4 import BeautifulSoup

url = 'https://results.eci.gov.in'

response = requests.get(url)

if response.status_code == 200:
    soup = BeautifulSoup(response.content, 'html.parser')
    results_table = soup.find('table', class_='table')

    # Loop through table rows and extract relevant data
    for row in results_table.find_all('tr'):
        cells = row.find_all('td')
        if len(cells) > 0:
            constituency = cells[0].text.strip()
            candidate = cells[1].text.strip()
            party = cells[2].text.strip()
            votes = cells[3].text.strip()

            print(f'Constituency: {constituency}, Candidate: {candidate}, Party: {party}, Votes: {votes}')

else:
    print('Failed to retrieve the page.')
