---
title: HTTP Cache
date: 2017-08-01 15:16:53
tags:
- Apache
---
Recently, I have a chance to revisit HTTP cache mechanism. Let's turn on the FireFox developer tool and launch http://www.google.com.hk:

{% img "/images/2017-07-28 10_18_23-Google.png"  %}

We will use the googlelogo_color_120x44dp.png for the illustration below.

For the first time, we can see  the server response with 200 (OK) for below images and the response header Last-Modified: "Thu, 08 Dec 2016 01:00:57 GMT":

{% img "/images/2017-07-27 15_05_28-Google.png"  %}

{% img "/images/2017-07-28 10_45_22-Google.png"  %}

Then, we reload (by Reload/Refresh or F5 the page. The server response with 304 (Not Modified) for the images:

{% img "/images/2017-07-28 10_41_41-Google.png"  %}

We can see the request header If-Modified-Since "Thu, 08 Dec 2016 01:00:57 GMT":

{% img "/images/2017-07-28 15_01_05-Google.png"  %}

The server will check whether the file has been modified since the date specified in the header. If no, then it will return 304 (Not Modified) and the browse will use the file in the local cache. Otherwise, it will return the new content with status 200 (OK),

If we press enter at address bar again:

{% img "/images/2017-07-28 14_40_21-Blogger_ Big Head Guy - Edit post.png"  %}

We can see there is no request to the server as the local browser cache of the images are still "fresh":

{% img "/images/2017-07-28 14_43_05-Google.png"  %}

ETag/If-None-Match is similar to Last-Modified/If-Modified-Since. The server will assign a tag (~version) for each file. If the file has been updated, then a new ETag will be generated. However, it is server dependent. If we have two web servers behind the load balancer, then same file will have different E-tag and may make the If-None-Match return false. Therefore, it is suggested to disable ETag.

If both an entity tag and a Last-Modified value have been provided by the origin server, SHOULD use both validators in cache-conditional requests. This allows both HTTP/1.0 and HTTP/1.1 caches to respond appropriately. (rfc2616)

We can use below headers to control the cache stored in browser / proxy servers:

{% img "/images/2017-07-28 15_32_31-Google.png"  %}

The max-age directive takes priority over Expires, so if max-age is present in a response.









