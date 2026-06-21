## Inspect the most frequent fields of work

* In the case of this population, we observe that although the main fields of work are physics and astronomy, there are many other fiels and it would be interesting to inspect specificities related to them.

```
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX p: <http://www.wikidata.org/prop/>
PREFIX ps: <http://www.wikidata.org/prop/statement/>
PREFIX pq: <http://www.wikidata.org/prop/qualifier/>

SELECT ?object ?objectLabel (COUNT(*) as ?eff)
WHERE
    {
    ### subquery adding the distinct clause
        {
        SELECT DISTINCT ?person_uri
        WHERE {
         {?person_uri wdt:P31 wd:Q5 ;
        p:P39 ?poste.
  ?poste ps:P39 wd:Q3044918 ;
         pq:P580 ?starttime.
  FILTER(YEAR(?starttime) >= 1958)}
            }
        } 
	
        ### The property P101 associates fields of work to persons
        ?person_uri wdt:P101 ?object.
        ?object rdfs:label ?objectLabel.
        FILTER(LANG(?objectLabel) = 'fr')
}  
GROUP BY ?object ?objectLabel 
ORDER BY DESC(?eff)
LIMIT 100
```


63 résultats sortis

### Most frequent fields of work

| object                                    | objectLabel                       | eff  |
| ----------------------------------------- | --------------------------------- | ---- |
| http://www.wikidata.org/entity/Q7163      | 	politique                       | 45   |
| http://www.wikidata.org/entity/Q1889      | diplomatie                        | 4    |
| http://www.wikidata.org/entity/Q7748      | droit                             | 3    |
| http://www.wikidata.org/entity/Q11030     | journalisme                       | 3    |
| http://www.wikidata.org/entity/Q115160290 | 	activité littéraire             | 3    |
| http://www.wikidata.org/entity/Q1791716   | domaine militaire                 | 3    |
| http://www.wikidata.org/entity/Q223569    | droits des femmes                 | 2    |


&nbsp;

### Query to get the data and import them into the database

```sparql

PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX p: <http://www.wikidata.org/prop/>
PREFIX ps: <http://www.wikidata.org/prop/statement/>
PREFIX pq: <http://www.wikidata.org/prop/qualifier/>

SELECT DISTINCT ?person_uri ?field_uri ?field_label
WHERE
    {
    ### subquery adding the distinct clause
        {
        SELECT DISTINCT ?person_uri
        WHERE {
         {?person_uri wdt:P31 wd:Q5 ;
        p:P39 ?poste.
  ?poste ps:P39 wd:Q3044918 ;
         pq:P580 ?starttime.
  FILTER(YEAR(?starttime) >= 1958)} 
            }
        } 
        ### The property P101 associates fields of work to persons
        ?person_uri wdt:P101 ?field_uri.
        ?field_uri rdfs:label ?field_label.
        FILTER(LANG(?field_label) = 'fr')
}  
LIMIT 100

```



#### Experimental : DO NOT USE
### Travail des parents
```sparql
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX p: <http://www.wikidata.org/prop/>
PREFIX ps: <http://www.wikidata.org/prop/statement/>
PREFIX pq: <http://www.wikidata.org/prop/qualifier/>

SELECT DISTINCT ?person_uri ?field ?fieldLabel ?parentField ?parentFieldLabel ?parentClass ?parentClassLabel
WHERE
    {
    ### subquery adding the distinct clause
        {
        SELECT DISTINCT ?person_uri
        WHERE {
         {?person_uri wdt:P31 wd:Q5 ;
        p:P39 ?poste.
  ?poste ps:P39 wd:Q3044918 ;
         pq:P580 ?starttime.
  FILTER(YEAR(?starttime) >= 1958)}  
            }
        } 
        ### The property P101 associates fields of work to persons
        ?person_uri wdt:P101 ?field.
        ?field rdfs:label ?fieldLabel.
        FILTER(LANG(?fieldLabel) = 'fr')
        OPTIONAL {
                # is instance of
                ?field wdt:P31 ?parentClass.
                        ?parentClass rdfs:label ?parentClassLabel.
                FILTER(LANG(?parentClassLabel) = 'fr')
            }
        OPTIONAL {
                # is subclass of
                ?field wdt:P279 ?parentField.
                        ?parentField rdfs:label ?parentFieldLabel.
                FILTER(LANG(?parentFieldLabel) = 'fr')
            }  
}  
# LIMIT 100

```




### Create a new table

* Download the result of this query as a CSV file
* Import it as a new table into the SQLite database
  * rename the column names during the import process 
* Inspect the imported data using the SQL scripts in the file [] 




## Occupations

```sparql
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX p: <http://www.wikidata.org/prop/>
PREFIX ps: <http://www.wikidata.org/prop/statement/>
PREFIX pq: <http://www.wikidata.org/prop/qualifier/>

SELECT ?person_uri ?occupation_label ?occupation_uri
WHERE
    {
    ### subquery adding the distinct clause
        {
        SELECT DISTINCT ?person_uri
        WHERE {
         {?person_uri wdt:P31 wd:Q5 ;
        p:P39 ?poste.
  ?poste ps:P39 wd:Q3044918 ;
         pq:P580 ?starttime.
  FILTER(YEAR(?starttime) >= 1958)}    
            }
        } 
	
        ### The property P106 associates occupations to persons
        ?person_uri wdt:P106 ?occupation_uri.
        ?occupation_uri rdfs:label ?occupation_label.
        FILTER(LANG(?occupation_label) = 'fr')
}  
ORDER BY ?person_uri ?occupation_uri
LIMIT 100
```

#### Experimental : DO NOT USE
### Occupation des parents
```sparql
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX p: <http://www.wikidata.org/prop/>
PREFIX ps: <http://www.wikidata.org/prop/statement/>
PREFIX pq: <http://www.wikidata.org/prop/qualifier/>

SELECT DISTINCT ?person_uri ?occupation ?occupationLabel ?parentOccupation ?parentOccupationLabel ?parentClass ?parentClassLabel
WHERE
    {
    ### subquery adding the distinct clause
        {
        SELECT DISTINCT ?person_uri
        WHERE {
         {?person_uri wdt:P31 wd:Q5 ;
        p:P39 ?poste.
  ?poste ps:P39 wd:Q3044918 ;
         pq:P580 ?starttime.
  FILTER(YEAR(?starttime) >= 1958)}  
            }
        } 
        ### The property P106 associates occupations to persons
        ?person_uri wdt:P106 ?occupation.
        ?occupation rdfs:label ?occupationLabel.
        FILTER(LANG(?occupationLabel) = 'fr')
        OPTIONAL {
                # is instance of
                ?occupation wdt:P31 ?parentClass.
                        ?parentClass rdfs:label ?parentClassLabel.
                FILTER(LANG(?parentClassLabel) = 'fr')
            }
        OPTIONAL {
                # is subclass of
                ?occupation wdt:P279 ?parentOccupation.
                        ?parentOccupation rdfs:label ?parentOccupationLabel.
                FILTER(LANG(?parentOccupationLabel) = 'fr')
            }  
}  
LIMIT 100

```