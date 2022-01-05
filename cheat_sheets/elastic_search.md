# Resources

GitHub : https://github.com/oliver006/elasticsearch-gmail <br/>YouTube Playlist 1: https://www.youtube.com/playlist?list=PLGZAAioH7ZlO7AstL9PZrqalK0fZutEXF <br/>YouTube Playlist 2: https://www.youtube.com/playlist?list=PLGZAAioH7ZlPjCOBhj3UYJlSVF6LNAeYE <br/>

# Why Elastic Search (ES)?

- Much more faster than SQL queries.
- To implement simple/fuzzy search.
- Open-source

# Setup ES + Kibana - Windows

Walkthrough: https://www.youtube.com/watch?v=OvBf09V0bf0&ab_channel=TechieLifestyle <br/>1. Download and Install ES : https://www.elastic.co/downloads/elasticsearch <br/>2. Download and Install Kibana : https://www.elastic.co/downloads/kibana <br/>3. Download and Install Java : https://www.oracle.com/java/technologies/downloads/#jdk17-windows <br/>4. Set Java environment variables and system variables. <br/>5. Run the elasticsearch.bat file <br/>6. Head to http://localhost:9200/ <br/>7. Run the kibana.bat file <br/>8. Head to http://localhost:5601/ <br/>

**Kibana:** A window into Elastic Search enables visual exploration and real-time analysis of data. <br/>

# Basic Concepts

- **Cluster:** Where the entire data will be stored.
- **Node:** Server - part of a cluster. (UUID - Universally Unique Identifier)
- **Shards:** Index is distributed into multiple shards across nodes for faster querying.
- **Replicas:** Replica of the Shards.
- **Index:** Similar to Database in RDBMS.
- **Documents with Properties:** One row in RDBMS in JSON format with the column titles as key is called *Document* in ES.
- **Types:** Table name in RDBMS is called *Type* in ES.
- **Mapping:** Sort of the template of different mappings in the Documents.

# API

## Document API

### Single Document API

- **Index API:** add or update a typed JSON document
  `POST <index_name>/doc/<id>` 
- **Get API:** getting a typed JSON document
  Search something =>`GET <index_name>/_search` or `GET <index_name>/_search?size=<size>` <br/>Check mapping => `GET <index_name>/_mapping`
  Check count => `GET <index_name>/_count`
- **Delete API:** deleting a typed JSON document
  `DELETE <index_name>/doc/<id>`
- **Update API:** updating a document based on the script provided
  `POST <index_name>/doc/<id>/_update { <script>}` 

### Multi Document API

- **Multi get API:** 

  ```json
  GET /_mget
  {
  	"docs":[
          {
          	"_index" : "employees-details",
          	"_type" : "doc",
          	"_id" : "101"
          }
          {
          	"_index" : "employees-details",
          	"_type" : "doc",
          	"_id" : "101"
          }
      ]
  }
  ```

- **Bulk API: **

  ```json
  POST _bulk
  {
  	"index" :
  	{
  		"_index" : "text",
  		"_type" : "_doc",
  		"_id" : "1"
  	}
  }
  {"field1" : "value1"}
  ```

- **Delete by query API:** 
  `DELETE <index_name>/_delete_by_query { <query> }`

- **Update by query API:** 
  `POST <index_name>/_update_by_query { <query>, <script> }`

- **Reindex API:** Create a copy of an index

  ```json
  POST _reindex
  {
      "source" : {
          "index" : ""
      },
      "dest" : {
          "index" : ""
      }
  }
  ```


## Search API

Allows to execute a search query and get back search hits that match the query. Two ways the query can be provided: (1) query string as parameter, or (2) using a request body. 

### Query using Query DSL (Domain Specific Language)

*Leaf query clause:* When we are looking for a particular thing.<br/>*Compound query clause:* When we are looking for a group of similar things.<br/>**General purpose:**

```json
GET <index_name>/_search
{
	"query": {
		"match": {
			"<field_name>" : "<value_name>"
		}
	}
	"from" : <position>
	"size" : <to_be_shown>
	"track_total_hits" : true
}
```

#### Full Text Query

- match : match the words
- match_phrase : match the exact phrase

```json
{
	"query": {
		"match_phrase": {
			"<field_name>" : {
                "query": "<value_name>",
                "slop": "<search_window>"
            }
		}
	}
}
```

- match_phrase_prefix : match the prefix of phrases

```json
{
	"query": {
		"match_phrase_prefix": {
			"<field_name>" : "<value_name>",
		}
	}
}
```

- match_all : returns everything
- query_string : 

```json
{
	"query": {
		"query_string": {
			"default_field" : "<field_name>",
            "fields" : ["<field_name_1>","<field_name_2>"],
            "query" : "*",
            "default_operator" :  <"AND" or "OR">
		}
	}
}
```

### Term Level Query

- Term Query

```json
{
	"query": {
		"term":{
            "<field_name>":{
                "value": "<value>"
            }
        }
	}
}
```

- Terms Query

```json
{
	"query": {
		"terms":{
            "<field_name>":["<value_1>,<value_2>"]
        }
	}
}
```

- Range Query

```json
{
	"query": {
		"range": {
            "<field_name>": {
                # greater than or equal to
            	"gte" : <value>,
                # less than or equal to
                "lte" : <value>
            }
        }
	}
}
```

- Prefix Query

```json
{
	"query": {
		"prefix": {
            "<field_name>": "<value>"
        }
	}
}
```

- Wildcard Query (REGEX allowed)

```json
{
	"query": {
		"wildcard": {
            "<field_name>": "*<partial_value>*"
        }
	}
}
```

### Compound Query

#### Bool Query

- must: (all query must return something)

```json
{
	"query": {
		"bool":{
            "must": [<normal_query_1>,<normal_query_2>],
        }
	}
}
```

- should: (query should return something)

```json
{
	"query": {
		"bool":{
            "should": [<normal_query_1>,<normal_query_2>],
            "minimun_should_match" : <value>
        }
	}
}
```

- filter: (filter using some conditions like range)

```json
{
	"query": {
		"bool":{
            "filter": {
                "range": {
                    "<field_name>": {
                        "gte" : "<value>",
                        "lte" : "<value>"
                    }
                }
            } 
        }
	}
}
```

