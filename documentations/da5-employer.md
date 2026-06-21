## Get the employers

* The property _P108 employer_ is quite frequent and adds an interesting relation to organisations that we will also use for graph analysis
* See [this document](../explore-employer.md) for a distribution of the most frequent employers



## Get the relations to the employers

```
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX p: <http://www.wikidata.org/prop/>
PREFIX ps: <http://www.wikidata.org/prop/statement/>
PREFIX pq: <http://www.wikidata.org/prop/qualifier/>

SELECT ?person_uri ?employer_uri
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
		
      ?person_uri wdt:P108 ?employer_uri.
}  
ORDER BY ?person_uri
# LIMIT 30
```
* execute the query on Wikidata or [QLever](https://qlever.dev/wikidata)
* download the result as a CSV file
* import the file as a 'import_person_employer' table into the database using DBeaver




## Get the list of the organisations

```
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX p: <http://www.wikidata.org/prop/>
PREFIX ps: <http://www.wikidata.org/prop/statement/>
PREFIX pq: <http://www.wikidata.org/prop/qualifier/>
SELECT DISTINCT ?organisation_uri ?organisation_label
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
		
        ?person_uri wdt:P108 ?organisation_uri.
        ?organisation_uri rdfs:label ?organisation_label.
        FILTER(LANG(?organisation_label) = 'fr')
}  
# LIMIT 30
```
* execute the query on Wikidata or [QLever](https://qlever.dev/wikidata)
* download the result as a CSV file
* import the file as an 'import_organisation' table into the database using DBeaver
  




## The 30 most frequent employers
https://qlever.dev/wikidata/LXm4xP



## Get the list of the organisations

```
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX p: <http://www.wikidata.org/prop/>
PREFIX ps: <http://www.wikidata.org/prop/statement/>
PREFIX pq: <http://www.wikidata.org/prop/qualifier/>
SELECT DISTINCT ?organisation_uri ?organisation_label 
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
		
        ?person_uri wdt:P108 ?organisation_uri.
        ?organisation_uri rdfs:label ?organisation_label.
        FILTER(LANG(?organisation_label) = 'fr')
}  
# LIMIT 30
```
* execute the query on Wikidata or [QLever](https://qlever.dev/wikidata)
* download the result as a CSV file
* import the file as an 'import_organisation' table into the database using DBeaver
  

## Get the list of the organisations' classes

```
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX p: <http://www.wikidata.org/prop/>
PREFIX ps: <http://www.wikidata.org/prop/statement/>
PREFIX pq: <http://www.wikidata.org/prop/qualifier/>
SELECT DISTINCT ?organisation_uri  ?organisation_class_uri ?organisation_class_label
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
		
        ?person_uri wdt:P108 ?organisation_uri.
        ?organisation_uri wdt:P31 ?organisation_class_uri.
        ?organisation_class_uri rdfs:label ?organisation_class_label.
        FILTER(LANG(?organisation_class_label) = 'fr')

}  
LIMIT 30
```
* execute the query on Wikidata or [QLever](https://qlever.dev/wikidata)
* download the result as a CSV file: import_organisations_and_classes.csv
* import the file as an 'import_organisations_classes' table into the database using DBeaver
  

  

### The first 20 most frequent employer classes

| class                                     | classLabel                                          | eff  |
| ----------------------------------------- | --------------------------------------------------- | ---- |
| http://www.wikidata.org/entity/Q875538    | public university                                   | 7636 |
| http://www.wikidata.org/entity/Q3918      | university                                          | 6258 |
| http://www.wikidata.org/entity/Q45400320  | open-access publisher                               | 5570 |
| http://www.wikidata.org/entity/Q62078547  | public research university                          | 4529 |
| http://www.wikidata.org/entity/Q31855     | research institute                                  | 4176 |
| http://www.wikidata.org/entity/Q23002054  | private not-for-profit educational institution      | 3358 |
| http://www.wikidata.org/entity/Q23002039  | public educational institution of the United States | 2622 |
| http://www.wikidata.org/entity/Q15936437  | research university                                 | 2456 |
| http://www.wikidata.org/entity/Q43229     | organization                                        | 2314 |
| http://www.wikidata.org/entity/Q902104    | private university                                  | 2302 |
| http://www.wikidata.org/entity/Q1767829   | comprehensive university                            | 2107 |
| http://www.wikidata.org/entity/Q1371037   | institute of technology                             | 1629 |
| http://www.wikidata.org/entity/Q615150    | land-grant university                               | 1505 |
| http://www.wikidata.org/entity/Q96888669  | academic publisher                                  | 1434 |
| http://www.wikidata.org/entity/Q115427560 | University of Excellence                            | 1307 |
| http://www.wikidata.org/entity/Q5341295   | educational organization                            | 1271 |
| http://www.wikidata.org/entity/Q1254933   | astronomical observatory                            | 1196 |
| http://www.wikidata.org/entity/Q1188663   | Colonial Colleges                                   | 1051 |
| http://www.wikidata.org/entity/Q163740    | nonprofit organization                              | 949  |
| http://www.wikidata.org/entity/Q38723     | higher education institution                        | 936  |




<br/>


### Examples of private companies

```
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX p: <http://www.wikidata.org/prop/>
PREFIX ps: <http://www.wikidata.org/prop/statement/>
PREFIX pq: <http://www.wikidata.org/prop/qualifier/>
SELECT ?employer ?employerLabel ?classLabel (COUNT(*) as ?eff)
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
		
      ?item wdt:P108 ?employer.
        ?employer rdfs:label ?employerLabel.
        FILTER(LANG(?employerLabel) = 'fr')
		?employer wdt:P31 ?class.
        ?class rdfs:label ?classLabel.
        FILTER(LANG(?classLabel) = 'fr')
		FILTER regex(?classLabel, '.*business.*|.*entrepise.*|.*compangnie.*') 
}  
GROUP BY ?employer ?employerLabel ?class ?classLabel 
ORDER BY DESC(?eff) ?employer 
LIMIT 20
```


| employer                                | employerLabel                                                | classLabel             | eff |
| --------------------------------------- | ------------------------------------------------------------ | ---------------------- | --- |
| http://www.wikidata.org/entity/Q217365  | Bell Labs                                                    | privately held company | 128 |
| http://www.wikidata.org/entity/Q49112   | Yale University                                              | production company     | 122 |
| http://www.wikidata.org/entity/Q238101  | University of Minnesota                                      | production company     | 71  |
| http://www.wikidata.org/entity/Q127990  | Australian National University                               | production company     | 53  |
| http://www.wikidata.org/entity/Q37156   | IBM                                                          | software company       | 48  |
| http://www.wikidata.org/entity/Q37156   | IBM                                                          | enterprise             | 48  |
| http://www.wikidata.org/entity/Q37156   | IBM                                                          | business               | 48  |
| http://www.wikidata.org/entity/Q37156   | IBM                                                          | public company         | 48  |
| http://www.wikidata.org/entity/Q37156   | IBM                                                          | technology company     | 48  |
| http://www.wikidata.org/entity/Q126824  | Institute of Physics and Power Engineering JSC               | business               | 47  |
| http://www.wikidata.org/entity/Q658192  | Vilnius University                                           | business               | 28  |
| http://www.wikidata.org/entity/Q1117048 | Commonwealth Scientific and Industrial Research Organisation | production company     | 27  |
| http://www.wikidata.org/entity/Q54173   | General Electric                                             | public company         | 23  |
| http://www.wikidata.org/entity/Q54173   | General Electric                                             | enterprise             | 23  |
| http://www.wikidata.org/entity/Q54173   | General Electric                                             | business               | 23  |
| http://www.wikidata.org/entity/Q734764  | University of New South Wales                                | production company     | 17  |
| http://www.wikidata.org/entity/Q170416  | Koninklijke Philips NV                                       | public company         | 15  |
| http://www.wikidata.org/entity/Q170416  | Koninklijke Philips NV                                       | enterprise             | 15  |
| http://www.wikidata.org/entity/Q170416  | Koninklijke Philips NV                                       | business               | 15  |
| http://www.wikidata.org/entity/Q632404  | Westinghouse Electric Corporation                            | business               | 13  |