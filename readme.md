# Elastic Extensible Search

An extensible search implementation for Elastic Search. 


## Configuration

Add the following to your project's config

```
---
Name: elastic_config
---
Injector:
  ElasticClient:
    class: Elastica\Client
    constructor:
      host_details: 
        host: elastic
        port: 9200
        # transport: AwsEs - this is needed for AWS search service compatibility; it adds credentials support
  ElasticaSearch:
    properties:
      searchService: %$ElasticaService
  ElasticaService:
    class: ExtensibleElasticService
    constructor:
      client: %$ElasticClient
      index: your_index_name

ExtensibleSearchPage:
  search_engine_extensions:
    ElasticaSearch: Elastic Search
  extensions:
    - ElasticaSearch
ExtensibleSearchPage_Controller:
  extensions:
    - ElasticaSearch_Controller

```

To add additional types for selection in an extensible search page config; note namespace slashes become underscores

```
---
Name: search_page_config
---
ElasticaSearch:
  additional_search_types:
    My_Namespaced_Class: Friendly Label

```

Run /dev/tasks/Symbiote-ElasticSearch-VersionedReindexTask


Note: Reindex will _ONLY_ reindex items that have the Searchable extension applied. There's also
a DataDiscovery extension that will grab taxonomy terms if available. 

```
---
Name: elastic_data_config
---
SiteTree:
  extensions:
    - Symbiote\ElasticSearch\ElasticaSearchable
    # for extra boosting options - Symbiote\Elastica\DataDiscovery
```



## Details

**How do I use the BoostTerms field?**

BoostTerms are used for subsequent querying, either direct through the builder or by the "Boost values" and
"Boost fields with field/value matches" options on the Extensible Search Page. 

The field hint states to use the word "important" in this field to boost the record super high in result sets. This
requires you to set the "Boost fields with field/value matches" to have an entry of

`BoostTerms:important` : `10` 

in the search page to boost records with that set. Additionally, set the "Boost values" for BoostTerms to be higher
than all other fields for any match to contribute highly. 

**Why the separate ElasticaSearchable extension?** 

The base Heyday Elastic module doesn't handle indexing of Versioned content directly; 
ElasticaSearchable provides a few overrides that take into account versioned content. 
