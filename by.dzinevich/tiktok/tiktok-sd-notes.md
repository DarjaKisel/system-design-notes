#TikTok
https://whimsical.com/tiktok-Tsuf6BroR1kyYUaUAebgnj

![](/Users/daryazinevich/code/system-design-notes/by.dzinevich/tiktok/tiktok-sd.png)

Let’s begin with some questions what is tiktok in general - mobile app, upload videos, share videos, like comment, scroll feed

Let’s focus on backend part rather than a mobile app just because there’re more tech features and details on that side

##Functional requirements:
- Top level buckets of work

###1, upload videos
An api client agnostic accept user data to upload videos
There’s a restriction, right? - yes, 30 seconds long, max 1 minute
Text associated with that? Tag ? Add comment\ text - test you can comments , text data
###2. View feed
   We’re aggregating the feed, the videos . Is it only the people I follow or + algorithm (recommended) - can I assume it is a mixture of those two
   Yes - for simplicity let’s focus only on people that you follow
   But it would be interesting to hear about how to handle recommended videos
- Should we comment/ like/ favourite videos
- Also follow pqarticalr creators
###3. Video interaction endpoint:
   Follow, like, comment, forward - all video interactions

##Non-functional requirements:
###1. Availability
   Assumption - highly available system
   Roughly - 99.99% available
   (It’s about budgeting computer resources) if availability is a top-level requirement
###1. Latency
   Mobile device - we can cache on th device itself
   Pull stuff on the background
   TBD - difff for different buckets
###1. Scale
   Rough estimate - ow many users - 1M active users a day
   Videos - 5 MB per video
   Each user upload - 2 per day = 1-MB per User
   User metadata - iK per user per day



Obviously, we’re going to have some sort of database to back this

Assumption - we’re going to have a relational database
So, uploading a video - will write to that table in that relational database

Difference between relational and nosql - linking many things together -
Nodal - unstructured data - free form data - like log data - not like you’re going to query this data and do many joins to it
It’s more like free form searching for keys and values
Relational database could be even more fast in some cases for some queries - \

Get feed - preload the feed in the background - ahead of time to not make user wait
Eg first 3 views as fast as we can

### Bottleneck of the system? If we x10 traffic
- Regions
- Versions, in regions
- Put a CDN. !!!!
- Also having load balancers in front off APIs
- ——————
  A content delivery network (CDN) in a group of geographically distributed servers that speed up the delivery of web content by bringing it closer to where users are
  Akamai!
  !!!!

Let’s say someone famous posts the video and everyone wants to see that video
The moment when this video is posted
The CDN grabs that video and caches it on the local CDN side and then users just grab it from the CDN
So that internet traffic is not hitting my storage (ny s3 for example) it’s only hitting CDN

###Shards!

Database sharding is a technique used to optimize database performance at scale.
It relies on separating data into logical chunks so that they can be separated and queried independently.
Learn more about sharding in this video.

A database shard, or simply a shard, is a horizontal partition of data in a database or search engine.
Each shard is held on a separate database server instance, to spread load.
Some data within a database remains present in all shards, but some appears only in a single shard.
