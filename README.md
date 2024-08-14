# Youtube_data_warehouse
Project on Youtube 
from googleapiclient.discovery import build

# My API key
api_key = 'AIzaSyDePmLb0Q4MKtmOwJ-zPQK9BM15u1Mbv0c'

# Create a build object for the YouTube API
youtube = build('youtube', 'v3', developerKey=api_key)

# channel ID of the channel want to retrieve
channel_id = 'UCH912uDFX3sIaPXMrJa9X_w'

# Call the API to get channel details
request = youtube.channels().list(
    part='snippet,statistics,contentDetails,status',
    id=channel_id
)

response = request.execute()
# print(response)
# Extract channel name from the response
if 'items' in response and len(response['items']) > 0:
    channel_name = response['items'][0]['snippet']['title']
    channel_id = response['items'][0]['id']
    subscribecount = response['items'][0]['statistics']['subscriberCount'] 
    videocount = response['items'][0]['statistics']['videoCount'] 
    views = response['items'][0]['statistics']['viewCount']
    channel_description = response['items'][0]['snippet']['description']    
    # Extract the uploads playlist ID
    uploads_playlist_id = response['items'][0]['contentDetails']['relatedPlaylists']['uploads']
    channel = response['items'][0]
    channel_status = channel['status']
    print('Channel Name:', channel_name)
    print('Channel id:', channel_id)
    print('subscribe count:', subscribecount)
    print('video count:', videocount)
    print('views:', views)
    print('channel description:', channel_description)
    print('uploads playlist id:', uploads_playlist_id)
    print('Channel status:',channel_status)
    # print('Channel id:', channel_id)
else:
    print('Channel not found or no response from the API.')

channel_type="Channel"
channel_description = channel_description[:300]
channel_status = channel_status.get('privacyStatus', 'No status found')
'''
import mysql.connector
from mysql.connector import Error
con=mysql.connector.connect(
    host="localhost",
    user="root",
    password="asdf1234",
    database="YOUTUBE_WAREHOUSE"
)
cursor = con.cursor()

 # Define the SQL query to insert data
insert_query = """INSERT INTO channel (channel_id, channel_name, channel_type,channel_views,channel_description,channel_status)VALUES (%s, %s, %s,%s, %s, %s)"""
        # Define the data to be inserted
data = (channel_id, channel_name, channel_type,views,channel_description,channel_status)

        # Execute the query
cursor.execute(insert_query, data)

        # Commit the transaction to save the changes
con.commit()
'''
from googleapiclient.discovery import build

# Your API key from the Google Developers Console
api_key = 'AIzaSyDePmLb0Q4MKtmOwJ-zPQK9BM15u1Mbv0c'

# Initialize the YouTube API client
youtube = build('youtube', 'v3', developerKey=api_key)

# Define the search query
search_query = channel_name  

# Make the API request to search for videos
search_response = youtube.search().list(
    q=search_query,
    part='snippet',
    maxResults=5,
    type='video'
).execute()

# Iterate through the video IDs and fetch comments
for item in search_response.get('items', []):
    # print(item)
    video_id = item['id']['videoId']
    video_title = item['snippet']['title']
    video_description = item['snippet']['description']
    published_at = item['snippet']['publishedAt']
    # view_count = item['statistics'].get('viewCount', 0)
    # like_count = item['statistics'].get('likeCount', 0)
    print(f"Video Title: {video_title}")
    print(f"Video ID: {video_id}")
    print(f"Video Decription: {video_description}")
    print(f"published at: {published_at}")
    # print(f"view count: {view_count}")
    # print(f"like count: {video_description}")
    '''
    import mysql.connector
    from mysql.connector import Error
    con=mysql.connector.connect(
    host="localhost",
    user="root",
    password="asdf1234",
    database="YOUTUBE_WAREHOUSE"
    )
    cursor = con.cursor()

    # Define the SQL query to insert data
    insert_query = """INSERT INTO playlist(playlist_id, playlist_name, channel_id)VALUES(%s, %s, %s)"""
        # Define the data to be inserted
    data = (uploads_playlist_id, playlist_name, channel_id)

    # Execute the query
    cursor.execute(insert_query, data)

    # Commit the transaction to save the changes
    con.commit()
    '''
    # Make the API request to get video details including tags, publishedAt, view count, and like count
    video_response = youtube.videos().list(
        part='snippet,statistics,contentDetails',
        id=video_id
    ).execute()

    # Extract details
    video_details = video_response['items'][0]
    # print(video_details)
    tags = video_details['snippet'].get('tags', [])
    published_at = video_details['snippet']['publishedAt']
    view_count = video_details['statistics'].get('viewCount', 0)
    like_count = video_details['statistics'].get('likeCount', 0)# favoriteCount,commentCount
    favorite_count = video_details['statistics'].get('favoriteCount', 0)
    comment_count = video_details['statistics'].get('commentCount', 0)
    thumbnail_url = video_details['snippet']['thumbnails']['high']['url']
    caption_status = video_details['contentDetails'].get('caption', 'false')
    duration = video_details['contentDetails']['duration']
    print(f"tags: {tags}")
    print(f"view count: {view_count}")
    print(f"like count: {like_count}")
    print(f"publish: {published_at}")
    print(f"favour count: {favorite_count}")
    print(f"comment count: {comment_count}")
    print(f"Thumbnail URL: {thumbnail_url}")
    print(f"Caption Status: {'Available' if caption_status == 'true' else 'Not available'}")
    print(f"Duration: {duration}")
    
    video_description=video_description[:300]
    dislike_count=like_count
    caption_status='Available'# {'Available' if caption_status == 'true' else 'Not available'}
    print(caption_status)
    print(uploads_playlist_id)
    # Convert to MySQL DATETIME format
    from datetime import datetime
    published_at = datetime.strptime(published_at, '%Y-%m-%dT%H:%M:%SZ').strftime('%Y-%m-%d %H:%M:%S')

    import mysql.connector
    from mysql.connector import Error
    con=mysql.connector.connect(
    host="localhost",
    user="root",
    password="asdf1234",
    database="YOUTUBE_WAREHOUSE"
    )
    cursor = con.cursor()

    # Define the SQL query to insert data
    insert_query = """INSERT INTO video(video_id, video_name, video_description,published_date,view_count,like_count,
    dislike_count,favorite_count,comment_count,duration,thumnail,caption_status,playlist_id)VALUES(%s, %s, %s,
    %s, %s, %s, %s, %s, %s, %s, %s, %s, %s)"""
        # Define the data to be inserted
    data = (video_id, video_title, video_description,published_at,view_count,like_count,dislike_count,favorite_count,
            comment_count,60,thumbnail_url,caption_status,uploads_playlist_id)

    # Execute the query
    cursor.execute(insert_query, data)

    # Commit the transaction to save the changes
    con.commit()
    
    # Make the API request to get comments for the video
    comments_response = youtube.commentThreads().list(
        part='snippet',
        videoId=video_id,
        maxResults=5,  # Limit to top 5 comments
        textFormat='plainText'
    ).execute()

    # Print comments
    for comment_item in comments_response.get('items', []):
        # print(comment_item)
        comment = comment_item['snippet']['topLevelComment']['snippet']['textDisplay']
        author = comment_item['snippet']['topLevelComment']['snippet']['authorDisplayName']
        comment = comment_item['snippet']['topLevelComment']['snippet']
        comment_id = comment_item['id']
        comment_text = comment['textDisplay']
        comment_author = comment['authorDisplayName']
        comment_published_at = comment['publishedAt']
        print(f"Comment ID: {comment_id}")
        print(f"Comment Text: {comment_text}")
        print(f"Comment Author: {comment_author}")
        print(f"Comment Published At: {comment_published_at}")
        #print(f"Comment by {author}: {comment}")
        print('---')

    print('===')

# preparing comment data
print("************* playlist data************")
query = """create table if not exists comment(
                            comment_id varchar(225) primary key,
                            comment_text text,
                            comment_author varchar(255),
                            comment_published_date datetime,
                            video_id varchar(255),
                            FOREIGN KEY (video_id) REFERENCES video(video_id)
                            )"""
print(comment_id)
print(comment_text)
print(comment_author)
print(comment_published_at)
print(uploads_playlist_id)
