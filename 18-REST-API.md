# REST
- REST => Representational State Transfer.
- Chỉ khác ở response.
- Data formats :
    + HTML : ejs
    + Plain Text: No Ui Assumptions, Data
    + XML : <name>Node.js</name> => Machine-readable bute relatively verbose; XML-parser needed.
    + JSON: Can easily be converted to JS
 
# Routing and HTTP Mehod.
- API Endpoint : POST /post , GET /posts,
- Methods :
    + get : get rosource
    + post : post to resource
    + put : put a resource (create or overwrite)
    + patch : Update parts of an existing Resource
    + delete : delete a resource.
    + OPTIONS : determine whether follow-up Request is allowed.
 
# REST Principes
- Uniform Interface : Clearly defined API Endpoints with clearly defined request + response data structure.
- Stateles Interactions : Server and client dont store any connection history, every request is handled seperately.
- Cacheable : servers may set caching headers to allow the client to cache responses.
- Client-Server : seperately.
- Layered System : Server may forward requestss to other APIs
- Conde on Demand : Ececutable code may be transsferred from server to client.

# Creating REST API
