### Fonction
```
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX p: <http://www.wikidata.org/prop/>
PREFIX ps: <http://www.wikidata.org/prop/statement/>
PREFIX pq: <http://www.wikidata.org/prop/qualifier/>
SELECT ?fonction ?fonctionLabel (COUNT(*) as ?eff)
WHERE
    {
    ### subquery adding the distinct clause
        {
        SELECT DISTINCT ?item
        WHERE {
         {?item wdt:P31 wd:Q5 ;
        p:P39 ?poste.
  ?poste ps:P39 wd:Q3044918 ;
         pq:P580 ?starttime.
  FILTER(YEAR(?starttime) >= 1958)}
            }
        } 
	
      ?item wdt:P39 ?fonction.
        ?fonction rdfs:label ?fonctionLabel.
        FILTER(LANG(?fonctionLabel) = 'fr')
}  
GROUP BY ?fonction ?fonctionLabel 
ORDER BY DESC(?eff)
```


### Occupation

```
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX p: <http://www.wikidata.org/prop/>
PREFIX ps: <http://www.wikidata.org/prop/statement/>
PREFIX pq: <http://www.wikidata.org/prop/qualifier/>
SELECT ?occupation ?occupationLabel (COUNT(*) as ?eff)
WHERE
    {
    ### subquery adding the distinct clause
        {
        SELECT DISTINCT ?item
        WHERE {
         {?item wdt:P31 wd:Q5 ;
        p:P39 ?poste.
  ?poste ps:P39 wd:Q3044918 ;
         pq:P580 ?starttime.
  FILTER(YEAR(?starttime) >= 1958)}
            }
        } 
	
      ?item wdt:P106 ?occupation.
        ?occupation rdfs:label ?occupationLabel.
        FILTER(LANG(?occupationLabel) = 'fr')
}  
GROUP BY ?occupation ?occupationLabel 
ORDER BY DESC(?eff)
```