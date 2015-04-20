---
layout: post
title: Redis Tips -- Use Sorted Set To Storage A Dynamically Changing List
tags: [english]
---

When I was developing an instant messenger (IM) system, I found that high concurrency is an essential feature while numerous clients are online chatting simultaneously. Therefore I use Redis to cache data in order to release pressure on database.

A typical scene of IM is chatting with someone else, as shown in the following figure.

![chat with someone](http://spetacular.github.io/images/2015-04-09/im-dialog.jpg)

This conversion contains several messages , which are stored in database in ascending order of their timing. As is shown in the following table.

	+----+------------------+--------+---------------------+
	| id | content          | isread | pubtime             |
	+----+------------------+--------+---------------------+
	|  1 | hello            |      1 | 2014-05-22 11:53:00 |
	|  2 | Hi!              |      1 | 2014-05-22 11:54:00 |
	|  3 | Nice to meet you |      1 | 2014-05-22 11:55:00 |
	|  4 | Me too           |      0 | 2014-05-22 11:56:00 |
	+----+------------------+--------+---------------------+

The "isread" field represents whether the message is read by someone.
# Store Data In A Key ?#
The following figure shows how we use Redis as cache container.

![traditional query](http://spetacular.github.io/images/2015-04-09/traditional-query.png)

When we need to retrieve some data , the first step is to check whether the data is in the cache container. If cache is hit, We get the data and return; If cache is missing , we query the data from database and store it in cache so that  we can get it faster next time. 

We can store a conversion in a key, so we can get all the containing messages from cache in one attempt.

But this method has several disadvantages:

1.  When a new message arrives, the cache has to be refreshed to add latest message. 
2.  When a message is set to Already Read status, the cache has to be destroyed to keep accuracy.
3.  When a message is deleted by its owner, the cache has to be refreshed again to remove that item.

The main reason for these disadvantages is that a conversion is a dynamically changing list. Each change will cause the cache out of date.

#Store List In Sorted Set#

What Sorted Set is ? I quote the following content from Redis official website ([See this](http://redis.io/topics/data-types#sorted-sets "Redis-sorted-sets")):

> Redis Sorted Sets are, similarly to Redis Sets, non repeating collections of Strings. The difference is that every member of a Sorted Set is associated with score, that is used in order to take the sorted set ordered, from the smallest to the greatest score. While members are unique, scores may be repeated.

We can store Ids of each message as index, and store each message separately, as shown in the following figure.


![Store List In Sorted Set](http://spetacular.github.io/images/2015-04-09/sorted-set-query.png)

We deal with each case as follows:

- Send Message and Receive Message. Add Id of message into the cache.
- Delete Message. Remove Id of message from the cache.
- Set Message Read. Leave the cache unchanged and Edit the corresponding message's cache.

#Code Sample#

Some Useful functions:

    //Add one or more members to a sorted set, or update its score if it already exists    
	function zAdd(key, score, member)
	
	//Return a range of members in a sorted set, by index
	function zRange(key, start, stop)	
	
	//Remove one or more members from a sorted set
	function zRem(key, member)

	//Set multiple keys to multiple values
	function mSet(key,value, ...)
	
	//Get the values of all the given keys
	function mGet(key, ...)



Store message Id as index, and its "pubtime" as score.

    pubtimestamp = strtotime(pubtime);
	zAdd(key,pubtimestamp,id);

Store messages with mSet Command:

	mSet(key,value, ...);

Retrieve messages like this:

	indexes = zRange(key, start, stop)
	msgs = mGet(indexes)
	