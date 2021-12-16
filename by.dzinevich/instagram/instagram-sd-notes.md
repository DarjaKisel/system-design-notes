# <b> Instagram </b>
https://whimsical.com/instagram-XuwZcqoo9JJNaC9FX3cnqp
![](/Users/daryazinevich/code/system-design-notes/by.dzinevich/instagram/instagram-sd.png)


##1. Requirements of our system

    1. Update images from a mobile client (what is it, native or not, other platform)  (storage space on a device)
    2. Allow users to follow other users and see their content
    3. Generate feed and show it to users  (how often should be updated, only people that you follow?)
    4. Scale 10 mill users (what sort of scale do we need to work at)

10 million:
- monthly ?
- 2 photos a month ?
- Each of those photos about 5 MB
- Location, caption, other metadata
- Ignore comments for now
  10^7 * 2*5MB
  10^8 = 100.000.000 MB = 100 TB per month = 1.2 PB year
  It’s going to grow

### This what our traffic is going to look like ^

Next thing:
- step back
  Think about the API or the data model of our system
  Start with the data model
  Since we know about what data we store

### 3. data types

1. Users
2. Photos
3. User following model (relationships)

Scotch 3 db tables

## Choice of database
Users are related to other users
Users can be followed by other users
Photos are related to users in many-to-one way
There s inheritance
— relational db

### There re other options
Whether your data inherits relations
Whether you would benefit from relational queries

In this case getting all photos for a user is a relational pattern - regular case

#### User table
id	PK, INT, serial
name	sting
Email address	String
location	string

#### Photos

| id  | PK, int, serial  |
|---|---|
| user_id  | FK referencing user.id  |
| caption  | string  |
| location  | String  |
| path  | String  |

Since images are large we’re  not going to store them in the db.

We’re going to store here a metadata.

And a reference to a distributed file system that is going to store and replicate the files

#### Followers

| User1  | FK referencing user.id  |
|---|---|
| Just  | FK referencing user.id  |

 a relational table

One direction of user following
NOT bi-directional
One user can follow another
But another will not follow the first one


#### Diagram:
Database metadata stores a path to a distributed object storage that can handle our images in a reliable and capable way
And we may get our data efficient and fast.

App Services layer is sort of a core of our system performing all the crud operations. The is the place where our clients send their requests
Serves as a system responding to clients performing operations to a database getting information and returning it to a user
There’re many kinds of such types of applications.
Usually the pattern is to start with a monolith web service, the application that responds to a many types of requests that are coming in
Then over time as your traffic scales up and you notice particular patterns that you need to handle such long processing that can be done as a async operation - splitting those into micro services (which is basically different operations the can be scaled independently form one another) to reach the best balance in server usage

### Access patterns for instagram in 10mln users:

1. Much more people viewing and reading than people uploading. We need both operations efficiently and support maybe 2 different access patterns that are happening in different times of a day..
2. read-heavy system - > probably we’re going to have a read-replica of the database that will allow us quickly read data from it without affecting our abilities to also write data and upload new images

We may have different patterns of reading writing, some requirements may be rerated to caching

We may implement caching between read server and metadata db in-memory storage (like redis)
This will allow us to retrieve data much much faster than requesting the db every time
Cache should be updated when we write to the db

We’re also are going to need a load balancer. As any system scales it’s very important that fundamental parts of system can handle load, otherwise requests may get lost, dropping requests, or may take a really long time

One of the most common patterns is scale horizontally. Mens not having just one app server - have many many servers located in different locations around the world.
We will serve requests to those servers in many different way so that each requests are utilised equally as much as possible, no overloaded servers

Separate load balancers, many proxies, routers etc…

#### Client
Diff browsers , different features that we want to support
Request will come to the load balancer
LB will look at the request and understand Which service it belongs to and route the request to one of the instances of that service
In read case it will be
-what image am I looking for, is it private, etc,, check the cache first to see if it has the image, then the image data is returned all the way back to the client (the image path on s3) and then the client with a separate request will. Get that image from the distributed object storage
In the upload case:
Upload the metadata  to the DB then  pass it on to the object storage - > upload cache

#### Feed for users:
Feed cache process ?
Handle by separate service
This feed get service will access our cache as well as our metadata database
It will schedule operations, e.g generate a new feed for each user on hourly basis
Talk to the db, understand which content is new, generate the feed (to heavy operation to do on-fly, that’s why we do it ahead of time) store it in our cache and the server that is responsible for reading will just read the cache 











 


