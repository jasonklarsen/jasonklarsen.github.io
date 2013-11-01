---
layout: post
title:  "Microservices and Heroku - CORS"
categories: blog resonance
---

In my [efforts to get a project skeleton]({% post_url 2013-10-23-resonance_0_0_1_a_skeleton %})
 in order for the [resonance][resonance] project, I stumbled into an interesting little problem with getting [micro-services][microservices] to work.

I have an independently deployed web client that wants to make AJAX-based REST calls to an independent HTTP service. So, that is two separately deployed web services. Ultimately, I plan to deploy each of these services to [Heroku][heroku], and therefore have each service with a separate URL. But, I found that this problem presents itself even in local testing with separate servers on different ports.

Let's say the web client is deployed on `localhost:8080`, and the REST service is deployed on `localhost:5000`. We then configure our AJAX call to make a request to `localhost:5000`, and run the code... but nothing happens. 

After some head scratching, I pull out the browser Developer Tools and watch the network traffic, and see this failure on the AJAX request to `localhost:5000`:

    No 'Access-Control-Allow-Origin' header is present on the requested resource. 
    Origin 'http://localhost:8080' is therefore not allowed access.

Ah! We are in the land of [same-origin policy][policy], and its evil twin, [cross-site scripting][xss]. After some researching, I stumble across the almost-standard way to get this to work, [cross-origin resource sharing (CORS)][CORS]. 

The basic idea is hinted at in the error above. We need to supply response headers from our REST service that say who is allowed to use the service. In particular, the browser is going to validate that the cross-origin response has a 'Access-Control-Allow-Origin' header that matches the origin of the original webpage. So, in my case, we have an request that is sent to localhost:5000 that has a request header:

    Origin: http://localhost:8080

The response needs to return with a header like:

    Access-Control-Allow-Origin: http:://localhost:8080

Excellent. I add some header logic to the REST service, deploy, and try again... Failed again. I now get the error:

    Request header field X-Requested-With is not allowed by 
    Access-Control-Allow-Headers.

A little further googling led me to [this site][corsExplained], and the realization that there is a bit more magic going on here. After a little more tinkering, my final set of returned headers look like this:

    Access-Control-Allow-Origin: http://localhost:8080
    Access-Control-Allow-Headers: X-Requested-With
    Access-Control-Allow-Methods: GET, POST, OPTIONS

Eureka! Now the two microservices are talking with one another, and I have a little more insight into the zany world of browser cross-origin policy.


[resonance]: https://github.com/jasonklarsen/resonance
[heroku]: https://www.heroku.com/
[policy]: http://en.wikipedia.org/wiki/Same_origin_policy
[xss]: http://en.wikipedia.org/wiki/Cross-site_scripting
[microservices]: http://yobriefca.se/blog/2013/04/29/micro-service-architecture/
[CORS]: http://en.wikipedia.org/wiki/Cross-Origin_Resource_Sharing
[corsExplained]: http://saltybeagle.com/2009/09/cross-origin-resource-sharing-demo/
