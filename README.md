# WikiData Plain SPARQL Client
This package allows to query the WikiData database. For example this package is intended for people who need to be able to query WikiData in plain SPARQL while staying whithin a Jupyter Notebook.

Currently data can be returned as a dataframe or as a map. See [usage](#usage) for examples
## Install
```bash
pip install wikidata_plain_sparql
```

## Usage
The module only has one function which is named query:
```python
# import the module
import wikidata_plain_sparql as wikidata

# query WikiData
wikidata.query('''
SELECT ?actor ?actorLabel
WHERE
{
  # Person of Interest has actor
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

Map example:
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

## Credits
- [@jelleschutter](https://github.com/jelleschutter/) - Development