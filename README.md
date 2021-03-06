# WikiData Plain SPARQL Client
This package allows to query the WikiData database. For example this package is intended for people who need to be able to query WikiData in plain SPARQL while staying whithin a Jupyter Notebook.

Currently data can be returned as a dataframe or as a map. See [examples](#examples)
## Install
```bash
pip install wikidata_plain_sparql
```

## Usage
The module only has one function which is named `query` which has two parameters:
- query: a sparql query
- view: should data be displayed as a map, function will return a data frame if `view` is `None` (see [examples](#examples) for map use)
```python
# import the module
import wikidata_plain_sparql as wikidata

wikidata.query(query, view=None)
```

## Examples
The following examples are also available on Google Colab and can be executed without any additional setup: [https://colab.research.google.com/github/jelleschutter/wikidata-plain-sparql/blob/assets/wikidata_plain_sparql_examples.ipynb](https://colab.research.google.com/github/jelleschutter/wikidata-plain-sparql/blob/assets/wikidata_plain_sparql_examples.ipynb)
### Data Frame
```python
# query WikiData
wikidata.query('''
SELECT ?actor ?actorLabel
WHERE
{
  # tv series "Person of Interest" has actor
  wd:Q564345 wdt:P161 ?actor.
  # actor has won a Golden Globe Award
  ?actor wdt:P166 wd:Q1011547.
  SERVICE wikibase:label { bd:serviceParam wikibase:language "en". }
}
''')
```
Which will result in the following pandas data frame:
| actor | actorLabel |
| --- | --- |
| http://www.wikidata.org/entity/Q3295000 | Sterling K. Brown |
### Map
The following query was copied from the offical [WikiData Examples](https://www.wikidata.org/wiki/Wikidata:SPARQL_query_service/queries/examples) and shows all places within 1km of the Empire State Building:
```python
# query WikiData
wikidata.query('''
#Places within 1km of the Empire State Building
SELECT ?place ?placeLabel ?location ?instanceLabel
WHERE
{
  wd:Q9188 wdt:P625 ?loc .
  SERVICE wikibase:around {
      ?place wdt:P625 ?location .
      bd:serviceParam wikibase:center ?loc .
      bd:serviceParam wikibase:radius "1" .
  }
  OPTIONAL {    ?place wdt:P31 ?instance  }
  SERVICE wikibase:label { bd:serviceParam wikibase:language "en" }
  BIND(geof:distance(?loc, ?location) as ?dist)
} ORDER BY ?dist
''', view='map')
```
Which will result in the following map:
![Map Example](https://github.com/jelleschutter/wikidata-plain-sparql/raw/assets/wikidata_empire_state_map_example.png)
## Credits
- [requests](https://pypi.org/project/requests/) - used for sending query to WikiData server
- [bokeh](https://pypi.org/project/bokeh/) - used for displaying map data
- [pandas](https://pypi.org/project/pandas/) - used for returning data as data frame
- [@jelleschutter](https://github.com/jelleschutter/) - development of this package