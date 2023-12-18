stmortem
Learning how to write an Incident Report, also referred to as a Postmortem. This postmortem follows the guidelines used closely by google engineers to file reports. The report is made up of five parts, an issue summary, a timeline, root cause analysis, resolution and recovery, and lastly, corrective and preventative measures. Lets review each of these parts in detail.

Issue Summary
short summary (5 sentences)
list the duration along with start and end times (include timezone)
state the impact (most user requests resulted in 500 errors, at peak 100%)
close with root cause
Timeline
list the timezone
covers the outage duration
when outage began
when staff was notified
actions, events, …
when service was restored
Root Cause
give a detailed explanation of event
do not sugarcoat
Resolution and recovery
give detailed explanation of actions taken (includes times)
Corrective and Preventative Measures
itemized list of ways to prevent it from happening again
what can we do better next time?
"""a recursive function that queries the Reddit API"""

import requests


def count_words(subreddit, word_list, after="", count=None):
    """all words are counted"""

    if after == "":
        count = [0] * len(word_list)

    url = f"https://www.reddit.com/r/{subreddit}/hot.json"
    headers = {'User-Agent': 'MyRedditBot/1.0'}
    params = {'after': after}
    response = requests.get(
        url,
        params=params,
        headers=headers,
        allow_redirects=False
    )
    if response.status_code == 200:
        data = response.json()

        for topic in data['data']['children']:
            for word in topic['data']['title'].split():
                for a in range(len(word_list)):
                    if word_list[a].lower() == word.lower():
                        count[a] += 1

        after = data['data']['after']
        if after is None:
            save = []
            for a in range(len(word_list)):
                for b in range(a + 1, len(word_list)):
                    if word_list[a].lower() == word_list[b].lower():
                        save.append(b)
                        count[a] += count[b]

            for a in range(len(word_list)):
                for b in range(a, len(word_list)):
                    if (count[b] > count[a] or
                            (word_list[a] > word_list[b] and
                             count[b] == count[a])):
                        # Original three-line swap
                        aux = count[a]
                        count[a] = count[b]
                        count[b] = aux
                        aux = word_list[a]
                        word_list[a] = word_list[b]

