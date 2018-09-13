# Random-Things
Birthday Special !!

To make this work, you need a token which you can obtain from Graph API Explorer with the appropriate permissions. The script assumes all posts after certain timestamp are birthday wishes.



 1
 2
 3
 4
 5
 6
 7
 8
 9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
# Thanking everyone who wished me on my birthday
 
import requests
 
import json
 
 
# Cube post time
 
AFTER = 1353233754
 
TOKEN = ' <insert token here> '
 
 
def get_posts():
 
    """Returns dictionary of id, first names of people who posted on my wall
 
    between start and end time"""
 
    query = ("SELECT post_id, actor_id, message FROM stream WHERE "
 
            "filter_key = 'others' AND source_id = me() AND "
 
            "created_time > 1353233754 LIMIT 200")
 
 
    payload = {'q': query, 'access_token': TOKEN}
 
    r = requests.get('https://graph.facebook.com/fql', params=payload)
 
    result = json.loads(r.text)
 
    return result['data']
 
 
def commentall(wallposts):
 
    """Comments thank you on all posts"""
 
    #TODO convert to batch request later
 
    for wallpost in wallposts:
 
 
        r = requests.get('https://graph.facebook.com/%s' %
 
                wallpost['actor_id'])
 
        url = 'https://graph.facebook.com/%s/comments' % wallpost['post_id']
 
        user = json.loads(r.text)
 
        message = 'Thanks %s :)' % user['first_name']
 
        payload = {'access_token': TOKEN, 'message': message}
 
        s = requests.post(url, data=payload)
 
 
        print "Wall post %s done" % wallpost['post_id']
 
 
if __name__ == '__main__':
 
    commentall(get_posts())
