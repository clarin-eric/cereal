# Frequently Asked Questions
## General

### What is the Curation Dashboard?
Curation Dashboard is a software bundle, which determines and grades
the quality of metadata harvested by CLARIN for language resources, in order to support their authors and
curators to improve the provided metadata quality. 
It consists of three parts: a stand alone application for reports generation (curation-core), a web application (curation-web) 
and another stand alone application for link checking (linkchecker). 

### What do VLO, record, collection, CMDI and other terms mean?
For a general overview of VLO, you can visit its [about page](https://vlo.clarin.eu/about). 
For more general info, [the CLARIN Metadata FAQ page](https://vlo.clarin.eu/about) is a good starting point.

## Curation dashboard
### What does the Curation Dashboard do exactly?
The Curation Dashboard processes publicly available CMDI records, 
collections and profiles. It grades them and gives them scores
based on different quality aspects. It also provides information on 
them and on what properties were graded.
         
### What is a 'collection' and where does the data come from?
A collection contains the metadata harvested by the VLO harvester
from a single 'endpoint', i.e. all the metadata from one repository and/or
CLARIN center.
This metadata is the output of the harvesting process, the result of which 
can be found at https://vlo.clarin.eu/.
     
### How often does the Curation Dashboard generate reports?
The core-application of Curation Dashboard generates the reports four times a week in the early morning hours CET.
    
### Is there a limit on file sizes?
Yes, the Curation Dashboard doesn't process files larger than 100 megabytes. 
Such files are ignored when collection reports are generated.
        
### How does scoring work?
     
A score value is calculated for profiles, instances and collections (which is the sum of its instance scores). 
The next two tables show the criteria on which the scoring is based on as well as the value set:
\
\
**Profile**
    
| Context      | Criteria                                                          | Value set |
|--------------|-------------------------------------------------------------------|-----------|
| Header       | Profile is public?                                                | {0, 1}    |
| Header       | Schema comes from Component Registry?                             | {0, 1}    |
| Facet        | Percentage of defined facets covered by profile                   | [0, 1]    |
| Cmd-concepts | Percentage of elements (except header and resources) with concept | [0, 1]    |
|              | **Sum**                                                           | [0, 4]    |

**Instance**
    
| Context        | Criteria                                                                                        | Value set     | Sum                |
|----------------|-------------------------------------------------------------------------------------------------|---------------|--------------------|
| File           | Valid file? [^1]                                                                                | {0, 1}        |                    |
|                | **File**                                                                                        |               | **{0, 1}**         |
| Header         | Valid schema location from attribute “schemaLocation” OR “noNamespaceSchemaLocation” available? | {0, 1}        |                    |
| Header         | MdProfile available and valid (against regular expression)?                                     | {0, 1}        |                    |
| Header         | MdCollectionDisplayName available?                                                              | {0, 1}        |                    |
| Header         | MdSelfLink available?                                                                           | {0, 1}        |                    |
| Header         | MdSelfLink unique? (only scored on collection level)                                            | {0, 1}        |                    |
|                | **Header**                                                                                      |               | **{1,..., 5}**     |
| Facet          | Percentage of of defined facets covered by instance                                             | \[0, 1]       |                    |
|                | **Facet**                                                                                       |               | **\[0, 1]**        |
| URL            | Percentage of valid links                                                                       | \[0, 1\] [^2] |                    |
|                | **URL**                                                                                         |               | **\[0, 1\]** [^2]  |
| XML            | Is the xml valid?                                                                               | {0, 1}        |                    |
| XML            | Percentage of populated elements                                                                | \[0, 1]       |                    |
|                | **XML**                                                                                         |               | **\[0, 2]**        |
|                | **Profile**                                                                                     |               | **\[0, 3]**        |
| Resource Proxy | Percentage of RP with mime type                                                                 | \[0, 1]       |                    |
| Resource Proxy | Percentage of RP with references                                                                | \[0, 1]       |                    |
|                | **Resource Proxy**                                                                              |               | **\[0, 2]**        |
|                | **Over all**                                                                                    |               | **\[0, 16\]** [^3] |

[^1] file size <= maximum file size AND valid schema location AND at least one resource link AND xml parsing messages with 
   status fatal or error < 3

[^2] the number of valid links is not scored for user upload. To me the scores comparable, we're weighting the score with the percentage of checked links. 
  If, for example we have checked only one line out of hundred, the maximum score can only be 0.01

[^3] because of the particular handling of the URL and the uniqueness of MdSelfLink in collections the maximum can vary between 14 and 16 
          
## Link checker
### What is the Link Checker?
The Link Checker is a stand-alone application which checks the availability of a resource at the addresses
referenced in the metadata. In practice, the resources are URLs (or more commonly links),
which can be checked via HTTP requests. The Link Checker then saves the responses to the requests in a database. 
The links are extracted from CMD Records within the collections.
Results of the checking can be directly viewed on the [Link Checker Statistics page](https://curation.clarin.eu/statistics)
and they also affect the overall score of the collections. 
        
### What technology is the Link Checker based?
The old implementation of the Link Checker was replaced by a new 
[codebase](https://github.com/clarin-eric/linkchecker), 
which is based on [Stormcrawler](http://stormcrawler.net/), 
which in turn is based on [Apache Storm](https://storm.apache.org/).

### How does the Link Checker work?
When the Curation Dashboard generates its collection reports, all resource links and self links
within the records are extracted and saved into
a database. The Link Checker then continuously checks these links and saves their results in the database. 
At the time of writing, there are approximately 3 million links, which are permanently checked in 50 parallel checking queues - one queue per host. 
The processing inside each queue is strictly serial, means the Link Checker sends one request after the other to the same host with respect of 
a crawl delay of one second. The crawl delay might be longer or shorter, depending on what is specified in the host's robots.txt, but remains in 
any case strictly serial. 
Hence, the period of time between two checks on the same link depends on the total number of links on the same host and the crawl delay, with respect 
of a minimum of 24h between two checks of the same link. 
The order of links in the checking queue is: 

- prioritized links first
- then the unchecked links
- further on in the order of the latest check
    
### What request method does the Link Checker use?
The Link Checker always sends a `HEAD` request first. If it is unsuccessful for whatever reason, it tries
a `GET` request. However, it doesn't read the response payload in case of `GET`. All info is extracted from
the status code and the headers.
    
### Does Link Checker follow redirects?
Yes it does. It even records how many redirects the link points to. 
However, there is a hard limit of 20 redirects.
    
### What categories are there?

Currently there are only 6 categories: 

1. Ok
2. Undetermined
3. Restricted access
4. Blocked by robots.txt
5. Broken
6. Invalid URL

### Is there a maximum response time?
There is indeed a maximum response time of 5 seconds. If the Link Checker doesn't receive any response within that period if time, 
the link is qualified as `Broken`.        
    
### The Curation Dashboard reports my links incorrectly. What should I do?
If you suspect the reason for the reports being wrong is the Link Checker and your links work fine,
please create an issue on our [github page](https://github.com/clarin-eric/curation-dashboard/issues).      
    
### The byte size of my link is shown as null but the link has a correct response body. What's wrong?
The HTTP Header `Content-Length` is taken as the single source of truth for the byte size. `HEAD`
requests don't contain a response payload by definition and Link Checker doesn't read the payload of
`GET` requests to save bandwidth and time. Therefore the only reliable source is the `Content-Length` header.
Please set it correctly wherever you can on your servers.
        
### The Link Checker is making more requests than my server can handle, or even causing high loads on my servers. What should I do?
The Link Checker respects any crawl delay specified `robots.txt` file of the target host. If nothing is set there, 
it respects a minimum crawl delay of one second between two requests on the same host. 
    
### How can I configure the access for the Link Checker in my robots.txt?
A typical configuration 
to authorize the Link Checker to access all resources with a specific crawl delay of 1 second between each request 
(which is currently the default when nothing else is set in robots.txt) would look like this:
```
User-agent: CLARIN-Linkchecker
Allow: /
Crawl-delay: 1
```   
As a starting point for information on how to configure the access to your resources in a more elaborated way we recommend the official site [Robots.txt Files](https://search.gov/indexing/robotstxt.html).

### How does the Link Checker identify himself?
The Link Checker sends the following User-Agent request header to identify himself:  
`"User-Agent" : "CLARIN-Linkchecker/<Link Checker version> (build with Apache Storm <Apache Storm version>/Storm Crawler <Storm Crawler version>; https://www.clarin.eu/linkchecker; linkchecker@clarin.eu)"`

### Where does "Expected Content Type" come from?
It is extracted from CMD Records. It is however not specified for all links.

### I have more questions. Where can I ask them?**
Feel free to [mail](mailto:curation@clarin.eu) us.
    
### Where can I report issues?
Feel free to [mail](mailto:curation@clarin.eu) us.
