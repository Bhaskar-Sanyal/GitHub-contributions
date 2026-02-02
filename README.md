# GitHub-contributions
Python script to get contributions from GitHub GraphQL API
import requests
import json
from datetime import datetime, timedelta

# Replace with your GitHub Personal Access Token (generate at https://github.com/settings/tokens with 'read:user' scope)
GITHUB_TOKEN = 'your_token_here'
USERNAME = 'Bhaskar-Sanyal'

# Calculate dates: last year exactly
to_date = datetime.now().strftime('%Y-%m-%dT%H:%M:%S')
from_date = (datetime.now() - timedelta(days=365)).strftime('%Y-%m-%dT%H:%M:%S')

query = '''
query($username: String!, $from: DateTime!, $to: DateTime!) {
  user(login: $username) {
    name
    login
    contributionsCollection(from: $from, to: $to) {
      totalContributions
      totalCommitContributions
      totalIssueContributions
      totalPullRequestContributions
      totalPullRequestReviewContributions
      contributionCalendar {
        totalContributions
      }
    }
  }
}
'''

url = 'https://api.github.com/graphql'
headers = {
    'Authorization': f'Bearer {GITHUB_TOKEN}',
    'Content-Type': 'application/json'
}

variables = {
    'username': USERNAME,
    'from': from_date,
    'to': to_date
}

response = requests.post(url, json={'query': query, 'variables': variables}, headers=headers)

if response.status_code == 200:
    data = response.json()
    contribs = data['data']['user']['contributionsCollection']
    print(f"User: {data['data']['user']['name']} (@{USERNAME})")
    print(f"Total contributions (last year): {contribs['totalContributions']}")
    print(f"Commits: {contribs['totalCommitContributions']}")
    print(f"Issues: {contribs['totalIssueContributions']}")
    print(f"PRs: {contribs['totalPullRequestContributions']}")
    print(f"PR Reviews: {contribs['totalPullRequestReviewContributions']}")
else:
    print(f"Error: {response.status_code} - {response.text}")
