### system design overview
- estimation: bandwidth , storage space, 720 p, 30FPS, 4MB/s
- long polling, web sockets, server sent
-   ajax polling, 
-   http long polling, server tries to respond within a timeout,
-   websocket: persostent communication between server and client, keep the socket open, 
-   server sent event SSE: 
- databases
-   sql: mysql, oracle, sqlite3, postgres, mariadb
-   nosql: not ACID compliance (atomicity, consistency, isolation, durability)
-       keyvalue: dynamo, redis, voldemort, 
-       document: mongodb
-       widecolumn: cassandra, HBase, 
-       graph databases: Neo4J, InfiniteGraph, 
-  partitioning: 
-       range based, 
-       hash based, 
-       round robin, 
-  indexes     
- CAP theorem:   consistency, availability, partition tolerance,          
- cache: memcache, redis, 
    - write through
    - write aroung
    - write back
    - eviction: FIFO, LIFO, LRU, LFU, MRU (most), RR (random replacement)

### different components of a system design
- load balancer: amazon elastic load balancer, 
- NoSql dbs: amazon dynamo db, cassandra, (read heavy, no join is needed)
- Sql Dbs: MySql, PostGl, (read / write ACID, join operations needed)
- Network protocols: communication handshake: HTTP(), 
- load balancer: / gatwway to communicate with app server, 
- 
    


### networking
- TCP / IP transmition control protocol, Internel protocol, 
- HTTP: uses TCP to comminuicate, 
- UDP: user datagram protocol, UDP / IP/ solvable high latency,
- SMTP: simple mail ransfer protocol sending email, 
- POP3: Port Office Protocol, 
- IMAP: Internel mesg ascc protocol, 
- HTTP: 
- FTP: file transfer protocol, 
- rtp : realtime transfer protocol, data loss ok, real time is important, 
- dns: domain name system, name to ip address, request-> resolver -> root server -> TLD server -> auth server

### Things to remember in system design
- monolith architecture: single point of failure, steep learning curve, good for small team
- microservice architecture: decoupled system, difficult to design, easy to make modifications and deploy, 
- nosql dbs: cassandra, mongodb, neo4j, amazon dynamodb, 
- load balancing at multiple levels, : least connection / least response / least bandwidth / round robin /
- caching: app server cache, content delivery network, cache eviction,
- data partitioning: horizontal : different tables; vertical: different db servers, problems: joining,
- proxy server:  
- sql : acid, join, 
- nosql: fast, key-value, rapid development, 
- CAP theorem: consistency, availbility, partition tolerance, 
- consistent hashing: key % n -> not horizontally scalable, not load balanced, 
- have a news feed table to see generated newsfeeds, 
- 
 
### instagram:
- photo sharing, public / private, 
- Requirements:
-   uplaod/ download/ view 
-   search title, follow others, 
-   generate feed, 
-   available, 200 ms latency, low consistency, releiable,    
- 100M user, 1M DAU, 2M photo / day, 23 photo / sec, file 200 KB, 1 day = 400 GB, 10 yrs 1425 TB, 
- database: photo + metadata, user + metadata, user -> follower map,   use MYSQL to store tables, and HDFS or S3 for 
- userphoto and userfollower: use wide column like cassandra, 
 
### facebook messenger
- one to one mesg, online offline status, persistent storage for chat, 
- realtime experience, consistent, available, 
- extend to group chat, push, 
- storage: 2TB / day, bandwidth : 2TB / day ~= 25 MB/ss
- different users logged into different server, 
- use push model, to minimize resource, client keeps connection to server, use HTTP + long polling or websocket, 
- server use hash table user -> connection, 
- 50M user, 50K concurrant, 10k servers,
- use a sequenct to maintain the correct view by the user, 
- frequent read / write / store data in wide column: HBase, 
- use a session service to maintain status, 
- on server failure, user automatically reconnect to another server, 
- group chat: groupid -> (user + time of join)

### dropbox / gdrive
- available, reliable, scalable, 
- upload / download, share, auto sync with device, ACID, version control, 
- files are stored as objects of 4MB chunks, keep local copy of metadata, 
- user profile, file storage, file metadata store, shared with, 
- client does sync with the server, 
-   upload, download, detect a file change, 
-   keep a local copy of the metadata, 
- update client using HTTP long polling,
- need a chunker,
- NoSQL + ACID, 
- clien -> mesg queue -> sync service,       

### netflix 
- stat: 110mil subscribers, 220 countries, 3B revenue/quarter, 250mil hrs of vide / day, 1B hrs video / week, 37% of peak US traffic,
- 2008: own data center -> AWS, monolith -> microservice to reduce points of failure,  
- client, backend, CDN ( content delivery network )
- EC2, AWS S3, business logic uservice, Hadoop-spark, video processsing and encoding, openConnect CDN, 
- click play button, get url from central server, checks for license/subscribtion etc, gets correct CDN location, 
- stores videos as multiple format + multiple resolution, 
- takes original video, breaks into chunks (~4s), buckets into (format, resolution) pair, 
- does transcoding / encoding done over 300 cpu, split into chunks, once done, pushed to S3 storage, 
- stores about ~ 1200 files per media content,
- stranger things has ~10k files, 
- determines if a movie is dense or sparse, based on sends data on demand,
- original data is host on amazon S3, cost effective service for static data,
- openconnect: parners with ISP to setup OpenConnect to cache frequent data into local servers, reducing traffic, 
- data from origianl server is copied over to openconnect (write operation) when network load is low,
- netflix: serves from 3 regions, if one fails, traffic gets rerouted through the other one, 
- uses amazon DynamoDB (amazon) and apache Cassandra (originally from Facebook)
- ref [video](https://www.youtube.com/watch?v=x9Hrn0oNmJM) 
- ref [blog](http://highscalability.squarespace.com/blog/2017/12/11/netflix-what-happens-when-you-press-play.html)


### AWS
- EC2 scalable computing, S3 scalable storage, 

### API rate limiter
- throttle based on number of requests received, 
- for given user, # of requests are fixed, 
- saves from DDOS attack, pw attempts, cridit card transaction, etc
- blocks, automation, security, 
- requerement: 
-   15 requests per second,
-   available / low latency, 
-   if exceeds, return error 'too many requests'
- throttle
-   hard
-   soft  
- algoritms: 
-   fixed window,   
-   sliding window
-   sliding window with counter (sliding window histogram)
- design: client: server, ratelimiter, logic server, 
- database: 
-   userid + startTime, 
-   use redis to store the table, use their lock function to avoid race condition, 
- extimation: 32 byte * 1M users,
- shard based on user id, find server using consistent hashing,
- handle video duplication:       


### design Youtube
- upload, share, search title, see stats, add view comments, 
- fast, available, real-time experience, 
- estimation: 
-   read 800M * 5 video / day, / 46k video / sec
-   write: 200 video / sec
-   1 min video ~ 50 MB, =? 25 GB .sec,
-   1TB outgoing bandwidth
- APIs
-   uploadVideo(), searchVideo(), streamVideo(id, bandwidth),
- upload video
-   queue, encode into multiple versions for diff resolutions, thumbnail generation, en   
-   files are stored in distributed filesystem, HDFS, 
-   user database, video database, video metadata storage, 
-   use MySql for DBs, 
-   
### Twitter
- Frontend: Ruby on rails for; Scala/Java: Backend
- Apache Thrift (Facebook) to communicate between different services, 
- MySql + FlockDB, Cassandra: storage + relationships; 
- Memcache: for caching data
- tweet, timeline , trend
- 1/1000 write vs read, 
- 140 chars
- Fanout: when someone tweets, the info is added to followers' timeline as well, 
- Celebrity tweets are pulled in at the final query time by end user.
- rate of a hashtag creates trend, 
- Trend: Apache strom / Heron, Kafka stream, Stream -> Filter -> Parse -> GeoTag -> window -> rank, 
- Filter trivial operators out, 
- Find how many hashtags within a time windows,
- Search timeline: Early bird -> parsed and categorized, scatter + gather 
- services: write, timeline, kafka stream trending, early bord for searching, 
- zookeper: maintains redis on cluster,
- gyzmoduck, tweetypie, 
- ref [video](https://www.youtube.com/watch?v=wYk0xPP_P_8&t=16s)
- ref [diagram](https://imgur.com/6TFkVL2)

### Facebook news feed design
- 300M DAU, each user follows say 300 friends + 200 groups, 1.5 TB of data, 18k requests per second, 
- 300M * 5 times a day / (24hr * 3600s): 17500 requests per seconds,
- feed storage: 500 posts * 1KB * 300M * 5 = 150 TB, 2000 machins with 100GB memory, 
- API design: GetNewsFeed(key, user, since), PostFeed(key, userid, itemid,)
- Database design: User, Entity, FeedItem, FeedMedia<- FeedItem
- system: webserer, app server, (user + post + media) databases + cache, generate feed service + cache, feed notification service, webserver, push / pull to user
- notification: pull / push / hybrid, for mobile do not do push, limited bandwidth, 
- feed ranking: time + like + views etc
- data partitioning


### Tiny URL
- 500M links / month, 100: 1 read / write, 
- New URLs	200/s
- URL redirections	20K/s
- Incoming data	100KB/s
- Outgoing data	10MB/s
- Storage for 5 years	15TB
- Memory for cache	170GB
- APIs
-   createURL() // timelimit, 
-   deleteURL()
- Database
-   user data
-   URL data, 
-   NOSQL database, use base 36 or base 64,  base 64, 8 char will be enough
-  pregenerate keys, keep in memory, as soon as one is used, update tanles, 
-  KSG: key generation service, have a replicae ready, 
-  tiny -> URL db: partition based on alphabate, 
-  partition based on their hash, 

### Yelp / map search 
- add / del/ edit entry
- search nearby places within a given caregory
- divide map into grids, 10x10 mile2, 200 million sq mile, 20 million grid, ~ 4 GB RAM, 
- for dense grids, use QuadTree to break grid into small pieces, when we reach a limit, 
- QuadTree: tree with 4 children, 
- given user location, keep searching for neighboring leaf nodes till desired result is found or max raduius is reached, 
- space to store quadtree : 12GB, 500M/500loc / grid = 1M grid points, 
- insert: add to database + insert into quadtree, 
- client -> load balance -> [app servers] -> [QuadTree servers] -> databases, 

### Ticketmaster
- 3 billion view, 10 million ticket booking / month
- 100 byte data, 2 GB / day, 4 TB / 5 yrs
- seatch(key, keywork, city, radius, startday, endday, sort_order)
- return data in JSON format, 
- ReserveSeatc(key, session, movie, shwo, seats[])
- return reservation status, 
- ActiveReservationSearch
- WaitForReservation service, 

### Design tiktok
- user profile, upload video, view video, Follower/ FOllowing
- create APIs, createUserProfile(), updateUSerProfile(), 
- uploadViero(key, userid, videoFile, metadata)
- getVideo(key, userid, videoid, bandwidth)
- showVide();
- estimates, definitely show estimates, 
- store user profile, in NOSql, read heavy, join is not needed, 
- profile picture / video 
- upload video : use multiple compression on video, one video will be converted into multiple video,
- put it in distributed file server, then the files are distribited over CDN
- S3, object store, 
- cache between the server and the filesystem, 
- use a microservice, 
- DB to keep uiser / video relation,but the actual files are stored into a separate file server, 
- async a video encoding, 
- break services into multiple services, use checksum or sha to find out if the video exists, 
- 

### Storage
- REDIS DB, in memory database,


### REDIS CACHE
- redis lab
- in memory distributed data structure / data base solution / used for large volume of data caching, 
- fast, modular, 
- written in C, super fast, 
- list, set, map etc
- cache can be on the service server, close to db, or somewhere in between, prefer a large distributed global cache in between 
- write through cache, write back, specially for password etc, 
- if data is changed, db broastcast a mesg, all cache updates, 
- eventual consistency, 
- thrashing, cache missing, 
- periodically write data on db, 
- LRU, LFU, eviction policy, 

### How to store images
- file 
- blob (binary large object)
- clob (char large object)
- prefer file base storage
- use CDN to experiade

### microservice
- client -> gateway -> profile service -> redirect the service, 
- image service + distributed file system to store images, decouple from profile authentication service, 
- chatting , http does not work, server client, receiver client need to poll all the time, 
- use a different protocol, web-socket/ tcp connection, xmpp connection
- use a sessions service to maintain connection between two users for direct messaging, 
- 
- 

### CACHE
  - eviction policy Least Recently Used (LRU), Least Frequently used (LFU), FIFO, 
 
### DNS
  - domain name server,
  - 3rd party, maps domain name to ip address, 
 
### CDN
  - content deliveray network, 
  - 
### Ruby on rails
- started in 2003
- MVC; Convention over configuration; DRY (Dont repeat yourself)


### pinterest
- share links, boards, share links, 
- public board / private board, 
- looking for links / no images at this point, 
- no limit on how many links, 
- non functional : assumptions
-   login / account management / security is already there/ 
- functional requirement
-   do we need a consistent view ?
-   add new boards
-   list all my boards, 
-   view boards, check permission, 
-   no cloning / like feature, 
-   need a timeline/ lag in timeline, 
-   eventual consistency, 
-   Share individually, 
-   list all boards shared with me, sort feature, 
- user: id, name, email, phone, dob, etc
- board: id, name, created by, owners, followers, create time, update time, 
- links: id, original url (1000 char), hash (128 chars), title, open graph details, 
- board-> link: board id _> link-id (uniq pair)
-     

### estimates
- 500 mil/ month, across globe. 
- 24M DAU, equal user across the day,  1M / hr -> reading, 0.1M user writing, read heavy service, 
- 0.1M / 3600 s => 3000 


### General system design blogs
- ref [blog](https://github.com/checkcheckzz/system-design-interview)
- ref [highscalability.com](http://highscalability.com/blog/category/example)
- ref [free course](https://systeminterview.com/scale-from-zero-to-millions-of-users.php)
- ref [on github](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design)


### key concepts: 
-  Scalability, Reliability, Availability, Efficiency, and Manageability,
-  Cassandra mongodb better scales horizontally, mysql scales vertically,
-  add redundancy to ensure consistemcy and availability, but at a cost, 


# webcrawler
- crawls web starting from seed page, copyright, mirror, search, scalable, 
- only html ? or media ? store ? or just search ?
- robot-exclusion, saved in robot.txt, couresey service, do not crawl, 
- 15Bpage / month = 6200 page / sec. 
- 15 B page * 100 KB = 1.5 petabute, 
- start from seed, get ip address, connect, get page, parse, extract info, gew new url, push new url to unvisited lists, 
- dfs within same website, bfs across muliple website, 
- find usrl, connet, extract info, remove duplicate, 
- 
- 
- 

## object oriented deisgn

### elevator
- passangers, 
- elevator cars, 
- doors, 
- buttons, 

