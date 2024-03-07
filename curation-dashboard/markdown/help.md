# Information about Curation Dashboard
## Introduction
Curation Dashboard is a service originally developed by the technical team of the [ACDH-CH](https://www.oeaw.ac.at/acdh) hosted and maintained by 
[CLARIN-ERIC](https://www.clarin.eu/). Its goal is to support CMD metadata authors and curators to improve quality of metadata for language resources. More information: 

* [CMD -Component Metadata](https://www.clarin.eu/content/component-metadata) - the CLARIN metadata framework. 
* [Code on Github](https://github.com/clarin-eric/curation-dashboard)
* [CLARIN-PLUS deliverable D2.1](https://office.clarin.eu/v/CE-2016-0742-CLARINPLUS-D2_1.pdf) - specification document for the Curation Dashboard from 2016, formulated in the context of [CLARIN-PLUS](https://www.clarin.eu/content/factsheet-clarin-plus) project. 

## Structure and functionality
Curation Dashboard consists of three modules:
* Curation-core
* Curation-web
* Linkchecker

## Curation-core
 This module does the actual analysis of individual CMD profiles, records and whole collections according to a number of quality criteria and generates reports and statistics which help discover potential problems that cause a lower metadata quality.

The reports are re-generated regularly (twice weekly) on the most recent dump of CMDI records as collected by the [CLARIN-VLO harvester](https://vlo.clarin.eu/oai-harvest-viewer/).

## Curation-web
This is the user facing web application. It offers four main functions: 
* on the fly [validation of individual profiles and metadata records](https://curation.clarin.eu/) (either by their URLs or uploading them as files)
* presenting pre-computed statistics for [CMD profiles](https://curation.clarin.eu/profile/table)
* presenting pre-computed statistics for [collections](https://curation.clarin.eu/collection/table)
* presenting pre-computed statistics for [link checking](https://curation.clarin.eu/statistics) and a continuously generated statistics in detail view
(hence values might differ if links of the provider have been checked in the meantime)

## Linchecker
It checks constantly and repeatedly (with respecting the robots.txt files) all the URLs contained in metadata records of the collections. Checking means sending HEAD and/or GET requests to URLs and saving the results (just the request meta-information, the headers, not the payload itself) in a database. Core module later uses these results to generate statistics and takes them into consideration when assessing the quality of the metadata. Additionally, the results are also used by the [CLARIN metadata catalogue, the VLO](https://vlo.clarin.eu/), to indicate the availability of a resource. 
This module is maintained in a [separate code-base](https://github.com/clarin-eric/linkchecker).

## Frequently Asked Questions and Feedback
You may go to our [faq](/faq) page, which tries to answer the most common questions. If your question is not answered, feel free to [mail](mailto:curation@clarin.eu) us. 