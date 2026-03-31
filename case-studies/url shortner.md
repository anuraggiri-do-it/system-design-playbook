# THE URL SHORTNER
## Introduction
The URL shortener is a web application OR A mechanism that allows users to
 shorten long URLs into more manageable and short url
## Features
- Shorten long URLs: Users can input a long URL and receive a shortened version that redirects
- Custom aliases: Users can choose custom aliases for their shortened URLs, making them more memorable and easier to share.
- Analytics: The application can provide analytics on the usage of shortened URLs, such as the number visited data with timestamps
### The Async Part — This is the Critical Insight
```
User clicks
    │
    ▼
API receives request
    │
    ├──► Fire analytics to Kafka queue ──► (processed later, separately)
    │         └── does NOT block
    │
    ▼
Redirect user instantly
```
- Expiration: Users can set expiration dates for their shortened URLs, after which they will no longer be accessible.
## Usages
- Social Media: Users can share shortened URLs on social media platforms where character limits exist, such as Twitter.
- Marketing: Businesses can use URL shorteners in their marketing campaigns to track the effectiveness of their
- Non Digital marketing  like billboard,visiting cards,  newspapers 
- Link Management: Individuals and organizations can use URL shorteners to manage and organize their links more efficiently.

## step  1
#  Understand the problem and establish design scope
- Identify the core features and functionalities of the URL shortener.
- Define the target audience and use cases for the application.
- Determine the scalability requirements and expected traffic volume.
- Consider security and privacy implications, such as preventing abuse and ensuring data protection.
## step 2
# Back of envolop calculation
The Order to State Assumptions in Interview
1. Daily writes        → "100M URLs created per day"
2. Read/write ratio    → "10:1, so 1B reads per day"
3. Data retention      → "Store for 10 years"
4. Short code length   → "7 characters — let me verify this is enough"
## step 3
In this section, 
we discuss the API endpoints, 
URL redirecting, and 
URL shortening flows.
### API Endpoints
# Here we need two api end points because through the 
 -  POST -  we can write the   long URL into the shortern url  
 @ POST /api/v1/data/shorten
# what happen 
 @ Client → POST(long URL) → Server
   Server → returns short URL
   1.Client sends long URL

   2.Server:
   2.a-Generates short code
   2.b.Stores mapping (short → long)
    
   3.Server responds with short URL

2. GET → Redirect to original URL
  GET /api/v1/{shortUrl}

What happens:
1.User clicks or opens short URL
2.Browser sends GET request
Server:
Looks up long URL
Responds with HTTP redirect (301/302)
 #  the http redirec5t have some meaning.  why? -because 
- 301 Moved Permanently: Indicates that the resource has been permanently moved to a new  long URL. Search engines will update their indexes to reflect the new URL.
# while
- 302 Found (or Moved Temporarily): Indicates that the resource is temporarily located at a long URL. by this method every request go through the  shortnening service  and  it has advantage to store every request made by the user to perform the anlytics  
# advantages and disdvantages of 301 and 302
| Status Code | Advantages | Disadvantages |
|-301         |less load to services provider server |only have one time record of rediorection|
|302          |can track every request to short url |more load to services provider server|

## STEP 3
# Database Design OR wAY TO STORE DATA
# USING THE HASH FUNCTION 
- Hash the long URL to generate a unique short code.
- Store the mapping of short code to long URL in a database.
- Ensure that the hash function produces a unique short code for each long URL to avoid collisions.