# What is ActionGenerator
ActionGenerator is a project focused on generating events of your choice. 
Imagine that you want to feed your search engine with millions of documents 
or you want to run stress test and see if your application can work under 
load for numerous hours. That’s exactly where you can use ActionGenerator. 
By developing your event type, a sink to consume those event and events 
source you are ready to go, the rest is done by ActionGenerator itself, so 
you only need to worry about the relevant part – your actions.

# ActionGenerator howto
You can find blog posts describing ActionGenerator at the following URL:
 * http://blog.sematext.com/2012/06/10/action-generator-part-one/
 * http://blog.sematext.com/2012/07/22/actiongenerator-part-two/

## Project Layout
Currently, the ActionGenerator project is divided into three modules:
 * ag-player      – common classes that enable implementing and running 
                    your own ActionGenerator for virtually any kind of 
                    actions.
 * ag-player-es   – ActionGenerator implementation for ElasticSearch.
 * ag-player-solr – ActionGenerator implementation for Apache Solr.

# Usage
The following action generators are available out of the box:

## Apache Solr
  * RandomQueriesSolrPlayerMain - generates random queries.
    You need to provide the following parameters:
    - Apache Solr core search handler URL address
    - name of the field queries should be run against
    - number of events
    For example:
    java -cp ag-player-solr-0.1.6-withdeps.jar com.sematext.ag.solr.RandomQueriesSolrPlayerMain http://localhost:8983/solr/documents name 1000

  * DictionarySolrPlayerMain - generates queries with the use of the provided dictionary.
    You need to provide the following parameters:
    - Apache Solr search handler URL address
    - name of the field queries should be run against
    - number of events
    - dictionary path
    For example:
    java -cp ag-player-solr-0.1.6-withdeps.jar com.sematext.ag.solr.DictionarySolrPlayerMain http://localhost:8983/solr/documents name 1000 dict.txt
  
  * DictionaryDataSolrPlayerMain - generates data and indexes them into provided instance.
    You need to provide the following parameters:
    - Apache Solr update handler URL address
    - number of events 
    - dictionary path
    - one or more fields and its types
    For example:
    java -cp ag-player-solr-0.1.6-withdeps.jar com.sematext.ag.solr.DictionaryDataSolrPlayerMain http://localhost:8983/solr/documents/update/ 100000 dict.txt id:numeric title:text likes:numeric
  
  * ComplexDataSolrPlayerMain - generates data and indexes them into Solr instance as DictionaryDataSolrPlayerMain.
    The main difference is that this player handles more complex data definition (see: Complex Data Definition below). 
    You need to provide the following parameters:
    - Apache Solr update handler URL address
    - number of events 
    - path to file with JSON data definition    
    For example:
    java -cp ag-player-solr-0.1.6-withdeps.jar com.sematext.ag.solr.ComplexDataSolrPlayerMain http://localhost:8983/solr/documents/update/ 100000 schema.json 

## ElasticSearch
  * SimpleEsPlayerMain - generates random queries.
    You need to provide the following parameters:
    - base URL
    - index name
    - name of the field queries should be run against
    - number of events
    For example:
    java -cp ag-player-es-0.1.6-withdeps.jar com.sematext.ag.es.SimpleEsPlayerMain http://localhost:9200/ documents text 1000

  * DictionaryEsPlayerMain - generates queries with the use of the provided dictionary.
    You need to provide the following parameters:
    - base URL
    - index name
    - name of the field queries should be run against
    - number of events
    - dictionary path
    For example:
    java -cp ag-player-es-0.1.6-withdeps.jar com.sematext.ag.es.DictionaryEsPlayerMain http://localhost:9200/ documents text 1000 dict.txt

  * DictionaryDataEsPlayerMain - generates data and indexes them into provided instance.
    You need to provide the following parameters:
    - base URL
    - index name
    - name of the field queries should be run against
    - number of events
    - dictionary path
    - one or more fields and its types
    For example:
    java -cp ag-player-es-0.1.6-withdeps.jar com.sematext.ag.es.DictionaryDataEsPlayerMain http://localhost:9200/ documents document 100000 dict.txt id:numeric title:text likes:numeric

  * BulkDictionaryDataEsPlayerMain - generates data and indexes them into provided instance.
    You need to provide the following parameters:
    - base URL
    - index name
    - name of the field queries should be run against
    - should data be sent in different thread (true) or the same (false)
    - bulk batch size
    - number of events
    - dictionary path
    - one or more fields and its types
    For example:
    java -cp ag-player-es-0.1.6-withdeps.jar com.sematext.ag.es.BulkDictionaryDataEsPlayerMain http://localhost:9200/ documents document false 100 100000 dict.txt id:numeric title:text likes:numeric

  * ComplexDataEsPlayerMain - generates data and indexes them into Elastic Search instance as DictionaryDataEsPlayerMain.
    The main difference is that this player handles more complex data definition (see: Complex Data Definition below). 
    You need to provide the following parameters:
    - base url
    - index name
    - name of the field queries should be run against
    - number of events 
    - path to file with JSON data definition    
    For example:
    java -cp ag-player-es-0.1.6-withdeps.jar com.sematext.ag.es.ComplexDataEsPlayerMain http://localhost:9200/ documents document 100000 schema.json 
 

## Supported Targets
Current implementation allows you to generate queries and data to the 
following targets:
 * Apache Solr 
 * ElasticSearch

There are also available external modules with support for other targets:
 * MongoDB (https://github.com/solrpl/ag-player-mongodb ) - currently support only for data generation.
 * CSV (https://github.com/solrpl/ag-player-csv) - support for simple types and simple CSV files

## Adding Support for Other Targets
In order to develop your own action generator, you need to provide the
following implementations:
 * Event class implementation that will represent a single event you want 
   to be generated. 
 * Sink implementation, which consumes your events. 
 * Source implementation that will be responsible for creating your
   events.

## Maven Artifacts
Maven artifacts of ActionGenerator project are published at 
https://oss.sonatype.org/content/groups/public/

* To use ActionGenerator you should add the following dependency to your project:
    <dependency>
        <groupId>com.sematext.ag</groupId>
        <artifactId>ag-player</artifactId>
        <version>0.1.6</version>
    </dependency>

* To use ActionGenerator for ElasticSearch add the following dependency to your project:
    <dependency>
        <groupId>com.sematext.ag</groupId>
        <artifactId>ag-player-es</artifactId>
        <version>0.1.6</version>
    </dependency>

* To use ActionGenerator for Apache Solr add the following dependency to your project:
    <dependency>
        <groupId>com.sematext.ag</groupId>
        <artifactId>ag-player-solr</artifactId>
        <version>0.1.6</version>
    </dependency>

## Complex Data Definition (using Datamodel library by http://solr.pl/)
ComplexDataSolrPlayerMain allows you to use expanded data definition.
Using JSON format the following information can be defined:
 * field name
 * type (currently supported: integer, long, date, string, text, array, object, enum)
 * probability - number from 0 to 100, indicating the probability that the 
                 defined field will be present in the generated document (100 
                 means that the field will be present in all the documents, 
                 50 means that about 50% of the documents will contain given field)
 * size (for arrays)
 
Example of input file: 

	{
	"data" : {
		"id" : {
			"type" : "identifier"
		},
		"tags" : {
			"type" : "array",
			"subtype" : {
				"type": "integer"
			},
			"size" : 10
		},
		"position" : {
			"type" : "object",
			"fields" : {
				"lon" : {
					"type" : "long"
				},
				"lat" : {
					"type" : "long"
				}
			},
			"probability" : 50
		},
		"created" : {
			"type" : "date"
		},
		"name" : {
			"type" : "string",
			"probability" : 30
		},
		"status" : {
			"type" : "enum",
			"available" : ["OPEN", "WAITING", "RUN", "CLOSED"]
		}
	}
	}

Output:

    <doc>
	<field name="id">30</field>
	<field name="tags">1061831906</field>
	<field name="tags">1823041322</field>
	<field name="tags">1043347384</field>
	<field name="tags">1435798929</field>
	<field name="tags">1863886272</field>
	<field name="tags">614448648</field>
	<field name="tags">899164045</field>
	<field name="tags">293936725</field>
	<field name="tags">1762090246</field>
	<field name="tags">1009759002</field>
	<field name="position.lon">-3400858544594038103</field>
	<field name="position.lat">-3581634897016771756</field>
	<field name="created">2011-05-15T20:57:28</field>
	<field name="status">OPEN</field>
    </doc>
	 

## License
Action Generator is released under Apache License, Version 2.0

## Contact
For any questions ping @sematext, @kucrafal, or @abaranau.
