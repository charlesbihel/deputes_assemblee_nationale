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
Nombre de résultats différents au sein de l'effectif relevé au 8 mars 2026 : 2789 fonctions
Fonctions principales : député français (3817), conseiller général (386), maire (290), conseiller régional (284), sénateur (278)

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
Nombre de résultats différents au sein de l'effectif relevé au 8 mars 2026 : 344 occupations
Occupations principales : personnalité politique (3776), cadre de la fonction publique (212), profession libérale ou assimilée (163), cadre admnistratif et commerciaux d'entreprises(163), avocat.e (162)
## Parti Politique


```
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX p: <http://www.wikidata.org/prop/>
PREFIX ps: <http://www.wikidata.org/prop/statement/>
PREFIX pq: <http://www.wikidata.org/prop/qualifier/>
SELECT ?partipolitique ?partipolitiqueLabel (COUNT(*) as ?eff)
WHERE
    {
    ### subquery adding the distinct clause
    ### La requête prend un parti par député (le premier adhéré?) et ne tient pas en compte les changements de partis possibles
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
	
      ?item wdt:P102 ?partipolitique.
        ?partipolitique rdfs:label ?partipolitiqueLabel.
        FILTER(LANG(?partipolitiqueLabel) = 'fr')
}  
GROUP BY ?partipolitique ?partipolitiqueLabel 
ORDER BY DESC(?eff)
```
Nombre de l'effectif des partis politiques au 8 mars 2026 : 187
Principaux partis politiques : Parti Socialiste (840) , Union pour un Parti Populaire (647), Rassemblement pour la République (472), Union pour la démocratie française (324), Renaissance (309)


```
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX p: <http://www.wikidata.org/prop/>
PREFIX ps: <http://www.wikidata.org/prop/statement/>
PREFIX pq: <http://www.wikidata.org/prop/qualifier/>
SELECT ?partipolitique ?partipolitiqueLabel (COUNT(*) as ?eff)
WHERE
    {
    ### subquery adding the distinct clause
    ### La requête prend en compte chaque passage d'un député au sein du parti sans identifier les changements chronologiques véritablement
        {
        SELECT ?item
        WHERE {
         {?item wdt:P31 wd:Q5 ;
        p:P39 ?poste.
  ?poste ps:P39 wd:Q3044918 ;
         pq:P580 ?starttime.
  FILTER(YEAR(?starttime) >= 1958)}
            }
        } 
	
      ?item wdt:P102 ?partipolitique.
        ?partipolitique rdfs:label ?partipolitiqueLabel.
        FILTER(LANG(?partipolitiqueLabel) = 'fr')
}  
GROUP BY ?partipolitique ?partipolitiqueLabel 
ORDER BY DESC(?eff)
```

## Lieu de naissance


```
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX p: <http://www.wikidata.org/prop/>
PREFIX ps: <http://www.wikidata.org/prop/statement/>
PREFIX pq: <http://www.wikidata.org/prop/qualifier/>
SELECT ?lieudenaissance  ?lieudenaissanceLabel (COUNT(*) as ?eff)
WHERE
    {
    ### subquery adding the distinct clause
    ###La requête ne permet pas d'obtenir l'ensemble de l'effectif par manque d'information
        {
        SELECT DISTINCT?item
        WHERE {
         {?item wdt:P31 wd:Q5 ;
        p:P39 ?poste.
  ?poste ps:P39 wd:Q3044918 ;
         pq:P580 ?starttime.
  FILTER(YEAR(?starttime) >= 1958)}
            }
        } 
	
      ?item wdt:P19 ?lieudenaissance .
        ?lieudenaissance rdfs:label ?lieudenaissanceLabel.
        FILTER(LANG(?lieudenaissanceLabel) = 'fr')
}  
GROUP BY ?lieudenaissance ?lieudenaissanceLabel
ORDER BY DESC(?eff)
```