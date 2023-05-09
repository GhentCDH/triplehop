# TripleHop

TripleHop is a platform that aims to facilitate collaborative research data creation and publication.

![TripleHop screenshot](triplehop_screenshot.png)

## General architecture

TripleHop constists of a separate [Front-end](https://github.com/GhentCDH/triplehop_frontend) ([Nuxt.js](https://nuxtjs.org/)) and [Back-end](https://github.com/GhentCDH/triplehop_backend) ([FastAPI](https://fastapi.tiangolo.com/)) implementation, who communicate with each other using GraphQL. Data is stored in a [PostgreSQL](https://www.postgresql.org/) database with the [Apache AGE](https://age.apache.org/) graph extension. [Elasticsearch](https://www.elastic.co/elasticsearch/) is used for the search pages with faceted navigation.

Some [Import Tools](https://github.com/GhentCDH/triplehop_import_tools) have been developed to facilitate the import of configuration and data files.

Nuxt.js allows Vue.js applications to be rendered on the server side on the initial request. This enables pages to be indexed by search engines and cached by web servers. After the first request, the Vue.js application is loaded in the browser and subsequent requests can be done by the Vue.js application, enhancing the user experience and minimizing network traffic.

### Initial detail page request

```mermaid
sequenceDiagram

    participant Browser
    participant FE as Front-End<br>(Nuxt.js)
    participant BE as Back-End<br>(FastAPI)
    participant DB as Database<br>(Postgresql with Apache AGE extention)
    participant SE as Search Engine<br>(Elasticsearch)

    activate Browser
    Browser ->> +FE: Get detail page
    FE ->> +BE: Get configuration<br>(GraphQL)
    BE ->> +DB: Get configuration<br>(SQL)
    DB -->> -BE: SQL response
    BE -->> -FE: GraphQL response
    FE ->> +BE: Get data<br>(GraphQL)
    BE ->> +DB: Get data<br>(Cypher)
    DB -->> -BE: Cypher response
    BE -->> -FE: GraphQL response
    FE -->> -Browser: HTML page
    deactivate Browser
```

### Subsequent detail page requests

```mermaid
sequenceDiagram

    participant Browser
    participant FE as Front-End<br>(Nuxt.js)
    participant BE as Back-End<br>(FastAPI)
    participant DB as Database<br>(Postgresql with Apache AGE extention)
    participant SE as Search Engine<br>(Elasticsearch)

    activate Browser
    Browser ->> +BE: Get another detail page<br>(GraphQL)
    BE ->> +DB: Get data<br>(Cypher)
    DB -->> -BE: Cypher response
    BE -->> -Browser: GraphQL response
    deactivate Browser
```

### Initial search page request

```mermaid
sequenceDiagram

    participant Browser
    participant FE as Front-End<br>(Nuxt.js)
    participant BE as Back-End<br>(FastAPI)
    participant DB as Database<br>(Postgresql with Apache AGE extention)
    participant SE as Search Engine<br>(Elasticsearch)

    activate Browser
    Browser ->> +FE: Get search page<br>(parameters in query string)
    FE ->> +BE: Get configuration<br>(GraphQL)
    BE ->> +DB: Get configuration<br>(SQL)
    DB -->> -BE: SQL response
    BE -->> -FE: GraphQL response
    FE ->> +BE: Get data<br>(JSON)
    BE ->> +SE: Get data<br>(JSON)
    SE -->> -BE: JSON response
    BE -->> -FE: JSON response
    FE -->> -Browser: HTML page
    deactivate Browser
```

### Subsequent search page requests

```mermaid
sequenceDiagram

    participant Browser
    participant FE as Front-End<br>(Nuxt.js)
    participant BE as Back-End<br>(FastAPI)
    participant DB as Database<br>(Postgresql with Apache AGE extention)
    participant SE as Search Engine<br>(Elasticsearch)

    activate Browser
    Browser ->> +BE: Get another search page<br>(JSON)
    BE ->> +SE: Get data<br>(JSON)
    SE -->> -BE: JSON response
    BE -->> -Browser: JSON response
    deactivate Browser
```

### Edit data

```mermaid
sequenceDiagram

    participant Browser
    participant FE as Front-End<br>(Nuxt.js)
    participant BE as Back-End<br>(FastAPI)
    participant DB as Database<br>(Postgresql with Apache AGE extention)
    participant SE as Search Engine<br>(Elasticsearch)

    activate Browser
    Browser ->> +BE: Edit data<br>(GraphQL mutation)
    BE ->> BE: Permission check
    BE ->> BE: Data validation
    BE ->> +DB: Update data<br>(Cypher)
    DB -->> -BE: Cypher response
    BE ->> BE: Determine impacted search items
    BE ->> +SE: Update documents<br>(JSON)
    SE ->> -BE: JSON response
    BE -->> -Browser: GraphQL response
    deactivate Browser
```

## Work in progress

The TripleHop platform is still a work in progress.

Currently implemented:

* Data model configuration using json files
* Basic exploration of data
* A part of the editing interface
* Data import using import scripts
* Limited GraphQL API

TO DO:

* Documentation
* Testing
* Data model configuration with a web interface
* Editing sources
* Data import using a web interface
* LOD presentation
* LOD mapping
* HDT export
* Plug-in system in the back-end and front-end, re-evaluation of the data model configuration format
* Graph visualizations
* Visual query interface

## Contributing

Please see our [contributing guidelines](CONTRIBUTING.md).

## Acknowledgements

TripleHop is and has been funded by

* [Clariah-VL](https://clariahvl.hypotheses.org/)
* [Cinema Belgica](https://www.cinemabelgica.be/)
* [Novel Echoes](https://research.flw.ugent.be/en/projects/novel-echoes)
