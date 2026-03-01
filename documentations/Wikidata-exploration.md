

Information disponible dans Wikidata concernant la population étudiée.


## Documentation concernant les requêtes SPARQL effectuées dans Wikidata

* Official SPARQL Endpoint: https://query.wikidata.org
* [QLever Wikidata](https://qlever.dev/wikidata)
  * This is an alternative experimental Wikidata Query service which is much faster (because it uses the [QLever triplestore technology](https://github.com/ad-freiburg/qlever)) but the data is not necessarily up to date. 
 
&nbsp;

* [SPARQL query service/queries](https://www.wikidata.org/wiki/Wikidata:SPARQL_query_service/queries): documentation.
* [Structure of statements in Wikidata](https://www.wikidata.org/wiki/Help:Statements)
* Query builder: https://query.wikidata.org/querybuilder/?uselang=en
* [Wikidata Dates](https://www.wikidata.org/wiki/Help:Dates)

&nbsp;

* [Wikidata SPARQL Tutorial](https://www.wikidata.org/wiki/Wikidata:SPARQL_tutorial)
* [W3C SPARQL Standard (and tutorial)](https://www.w3.org/TR/sparql11-query/)



 

&nbsp;




## Inspection des notices

On décide de prendre la population des députés depuis la création de l'Assemblée Nationale française créée en 1958 sous la 5e république.
On effectue les recherches à partir de la notice de l'organe politique :
[National Assembly](https://www.wikidata.org/wiki/Q193582)

On choisit quelques personnes et on inspecte leurs notices dans Wikidata afin d'observer quelles propriétés permettent de retrouver la population.

Par exemple:

* [Mikaele Seo](https://www.wikidata.org/wiki/Q112651641)
  * Noter propriétés 'member of', 'party' et 'position held'
  * Cf. sa [notice dans DBpedia](https://dbpedia.org/page/Mikaele_Seo)
  * Noter les différents mandats entre assemblé territoriale de Walis et Futuna et l'Assemblée nationatiale française
* [Bérenger Cernon](https://www.wikidata.org/wiki/Q127255949)


On retient quelques propriétés qui permettent de retrouver toute la population:
* [position held](https://www.wikidata.org/wiki/Property:P39)

## On effectue des requêtes pour vérifier quels effectifs sont disponibles et de qui il s'agit


### Effectifs concernant 'position held'

Effectifs relevés au 23 février 2026 : 13225

```
SELECT (COUNT(*) as ?eff)
WHERE {
    ?item wdt:P31 wd:Q5;  # Any instance of a human.
    wdt:P39 wd:Q3044918  # member of the French National Assembly
}  
```

[member of the French National Assembly](https://www.wikidata.org/wiki/Q3044918) is part of : 

[National Assembly](https://www.wikidata.org/wiki/Q193582) qui représente la chambre basse du Parlement français sous la 5e république à partir de 1958.

[National Assembly of the 4th Republic](https://www.wikidata.org/wiki/Q2867087) qui représente la chambre du parlement bicaméral français de la 4e république entre 1946 et 1958.

[Chamber of Deputies](https://www.wikidata.org/wiki/Q320283) qui représente la chambre basse française de 1830 à 1848 et de 1875 à 1942.

## Nécessite d'utiliser des propriétés pour affiner la recherche et restreindre les mandats à la 5e République

Propriétés dans position held : [member of the French National Assembly](https://www.wikidata.org/wiki/Q3044918) : 

[start time](https://www.wikidata.org/wiki/Property:P580)
choisir à partir de 1958


### Nombre de l'effectif de députés pour la 5e république



A noter : La commande ne permet pour l'instant de distinguer le cumul des mandats et donc la répitition des individus au sein de l'effectif

Please note that SPARQL operates in a layered manner: the innermost layer is executed first and the result set is sent to the next layer up.

```
SELECT (COUNT(*) as ?eff)
WHERE {
  ?item wdt:P31 wd:Q5 ;
        p:P39 ?poste.
  ?poste ps:P39 wd:Q3044918 ;
         pq:P580 ?starttime.
  FILTER(YEAR(?starttime) >= 1958)
}
 ```
Effectifs relevés au 1er mars 2026 : 8655



### Distinguer les individus au sein de l'effectif

```
SELECT (COUNT(DISTINCT ?item) as ?eff)
WHERE {
  ?item wdt:P31 wd:Q5 ;
        p:P39 ?poste.
  ?poste ps:P39 wd:Q3044918 ;
         pq:P580 ?starttime.
  FILTER(YEAR(?starttime) >= 1958)
}
```
Nombre d'individus différents au sein de l'effectif relevé au 1 mars 2026 : 3819


### Les individus


```
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

SELECT DISTINCT?item ?itemLabel
WHERE {
  ?item wdt:P31 wd:Q5 ;
        p:P39 ?poste.
  SERVICE wikibase:label {
# Retourne les labels en français
bd:serviceParam wikibase:language "fr" }
  ?poste ps:P39 wd:Q3044918 ;
         pq:P580 ?starttime.
  FILTER(YEAR(?starttime) >= 1958)
}
  ```  
  Les noms des 3819 députés apparaissent

    ### Two ways of getting labels
    # SERVICE wikibase:label { bd:serviceParam wikibase:language "en" }

    ## This is useful for query from external tool
    ?item rdfs:label ?itemLabel.
    FILTER(LANG(?itemLabel) = 'en')



### Number of individuals without English label

```
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
SELECT (COUNT(*) as ?eff)
WHERE
    {
    ### sous requête qui ajoute la clause distinct
        {
        SELECT DISTINCT ?item ?itemLabel ?year
        WHERE {
        ?item wdt:P31 wd:Q5; 
              wdt:P569 ?birthDate.
        BIND(REPLACE(str(?birthDate), "(.*)([0-9]{4})(.*)", "$2") AS ?year)
        FILTER(xsd:integer(?year) > 1780 && xsd:integer(?year) < 1981)# Any instance of a human.
            {?item wdt:P106 wd:Q11063}
            UNION
            {?item wdt:P101 wd:Q333} 
            UNION
            {?item wdt:P106 wd:Q169470}
            UNION
            {?item wdt:P101 wd:Q413}            
        MINUS {?item rdfs:label ?itemLabel.
            FILTER(LANG(?itemLabel) = 'en')
            }
            }
        }        
    }  
 ```


### Individuals without English label

Inspect individuals' cards and observe their properties

```
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
SELECT ?item ?year (group_concat(?iso_lang ; separator = ',') as ?langs) (max(?itemLabel) as ?maxLabel)
WHERE
    { 
       { SELECT DISTINCT ?item ?year
        WHERE {
        ?item wdt:P31 wd:Q5; 
              wdt:P569 ?birthDate.
        BIND(REPLACE(str(?birthDate), "(.*)([0-9]{4})(.*)", "$2") AS ?year)
        FILTER(xsd:integer(?year) > 1780 && xsd:integer(?year) < 1981)# Any instance of a human.
            {?item wdt:P106 wd:Q11063}
            UNION
            {?item wdt:P101 wd:Q333} 
            UNION
            {?item wdt:P106 wd:Q169470}
            UNION
            {?item wdt:P101 wd:Q413}            
        MINUS {?item rdfs:label ?itemLabel.
            FILTER(LANG(?itemLabel) = 'en')
            }
            }
        }        
      
        ?item rdfs:label ?itemLabel. 
		BIND(LANG(?itemLabel) as ?iso_lang)
            
       }
	   GROUP BY ?item ?year
	   ORDER BY ?item
	   LIMIT 100
 ```



### Persons filtered by birth year

Par exemple ici les astronomes et physiciens du 19e et 20e siècles 


```
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

SELECT DISTINCT ?item ?itemLabel ?year
        WHERE {
        ?item wdt:P31 wd:Q5; 
              wdt:P569 ?birthDate.
        BIND(REPLACE(str(?birthDate), "(.*)([0-9]{4})(.*)", "$2") AS ?year)
        FILTER(xsd:integer(?year) > 1780 && xsd:integer(?year) < 1981)# Any instance of a human.
            {?item wdt:P106 wd:Q11063}
            UNION
            {?item wdt:P101 wd:Q333} 
            UNION
            {?item wdt:P106 wd:Q169470}
            UNION
            {?item wdt:P101 wd:Q413}            
        ?item rdfs:label ?itemLabel.
        FILTER(LANG(?itemLabel) = 'en')
            }
ORDER BY ?item            
LIMIT 20
```
NB: il peut y avoir des doublons si les dates de naissance sont multiples. La clause DISTINCT permet d'enlever les doublons il faut toutefois enlever la variable *?birthDate* de la sortie et laisser seulement l'année




## Lister les propriétés disponibles avec effectifs


!!! filtrer par discipline et période et voir différentes propriétés

### Sortantes

Cf. [sur cette page](./Wikidata-liste-proprietes-population.md) les listes de propiétés qui résultent de cette requête
```
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX wikibase: <http://wikiba.se/ontology#>
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
SELECT ?p ?propLabel ?eff
WHERE {
{
    SELECT DISTINCT  ?p  (count(*) as ?eff)
    WHERE {
        ?item wdt:P31 wd:Q5; 
             wdt:P569 ?birthDate.
        BIND(REPLACE(str(?birthDate), "(.*)([0-9]{4})(.*)", "$2") AS ?year)
        FILTER(xsd:integer(?year) > 1780 && xsd:integer(?year) < 1981)# Any instance of a human.
            {?item wdt:P106 wd:Q11063}
            UNION
            {?item wdt:P101 wd:Q333} 
            UNION
            {?item wdt:P106 wd:Q169470}
            UNION
            {?item wdt:P101 wd:Q413}.
			?item ?p ?o.
        }
		GROUP BY ?p
    }
    ?prop wikibase:directClaim ?p .

    ?prop rdfs:label ?propLabel.
        FILTER(LANG(?propLabel) = 'en')
    }  
ORDER BY DESC(?eff) 
```

NB Noter qu'il peut y avoir des problèmes de time-out, la requête est trop longue et on a un message d'erreur.
<br/>
Dans ce cas il faut restreindre la période ou limiter le nombre de clauses UNION et décomposer la requête en différentes parties.

On exporte ensuite cette liste sous forme d'une _table HTML_ afin de documenter la suite des opérations. On ouvre la page HTML avec VS Code, on peut mettre en forme avec la commande (click droit) _format document_, puis on copie seulement la partie 'table' depuis la balise &lt;table&gt; jusqu'à &lt;/table&gt;, balises comprises, et on la colle dans un nouveau document Markdown, cf. [Wikidata-liste-proprietes-population.md](Wikidata-liste-proprietes-population.md)


On pourra prendre des notes concernant les opérations effectuées sur les différentes propriétés directement dans ce document et documenter ainsi les choix effectués.

## Add the subpopulation code

```
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX wikibase: <http://wikiba.se/ontology#>
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
SELECT ?p ?propLabel ?eff ?itemType
WHERE {
{
    SELECT DISTINCT  ?p  (count(*) as ?eff) ?itemType
    WHERE {
        ?item wdt:P31 wd:Q5; 
             wdt:P569 ?birthDate.
        BIND(REPLACE(str(?birthDate), "(.*)([0-9]{4})(.*)", "$2") AS ?year)
        FILTER(xsd:integer(?year) > 1780 && xsd:integer(?year) < 1981)# Any instance of a human.
            {?item wdt:P106 wd:Q11063.
			BIND ('astronomer' as ?itemType)}
            UNION
            {?item wdt:P101 wd:Q333.
			BIND ('astronomer' as ?itemType).} 
            UNION
            {?item wdt:P106 wd:Q169470.
			BIND ('physicist' as ?itemType)}
            UNION
            {?item wdt:P101 wd:Q413.
			BIND ('physicist' as ?itemType)}
			.
			?item ?p ?o.
        }
		GROUP BY ?p ?itemType
        ## limit to more frequent properties
		HAVING(?eff >= 100)
    }
    ?prop wikibase:directClaim ?p .

    ?prop rdfs:label ?propLabel.
        FILTER(LANG(?propLabel) = 'en')
    }  
ORDER BY ?propLabel ?itemType 
```

##  Same query but grouped


```
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX wikibase: <http://wikiba.se/ontology#>
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>

SELECT ?p ?propLabel (max(?eff) as ?max_eff) 
(group_concat(concat(str(?eff), ' ', ?itemType); separator=" | ") as ?eff_type)
WHERE {


SELECT ?p ?propLabel ?eff ?itemType
WHERE {
{
    SELECT DISTINCT  ?p  (count(*) as ?eff) ?itemType
    WHERE {
        ?item wdt:P31 wd:Q5; 
             wdt:P569 ?birthDate.
        BIND(REPLACE(str(?birthDate), "(.*)([0-9]{4})(.*)", "$2") AS ?year)
        FILTER(xsd:integer(?year) > 1780 && xsd:integer(?year) < 1981)# Any instance of a human.
            {?item wdt:P106 wd:Q11063.
			BIND ('astronomer' as ?itemType)}
            UNION
            {?item wdt:P101 wd:Q333.
			BIND ('astronomer' as ?itemType).} 
            UNION
            {?item wdt:P106 wd:Q169470.
			BIND ('physicist' as ?itemType)}
            UNION
            {?item wdt:P101 wd:Q413.
			BIND ('physicist' as ?itemType)}
			.
			?item ?p ?o.
        }
		GROUP BY ?p ?itemType
		HAVING(?eff >= 100)
    }
    ?prop wikibase:directClaim ?p .

    ?prop rdfs:label ?propLabel.
        FILTER(LANG(?propLabel) = 'en')
    }  
	}
	GROUP BY ?p ?propLabel
     ORDER BY desc(?max_eff)

```



### Entrantes
```
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX wikibase: <http://wikiba.se/ontology#>
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
SELECT ?p ?propLabel ?eff
WHERE {
{
    SELECT DISTINCT  ?p  (count(*) as ?eff)
    WHERE {
        ?item wdt:P31 wd:Q5; 
             wdt:P569 ?birthDate.
        BIND(REPLACE(str(?birthDate), "(.*)([0-9]{4})(.*)", "$2") AS ?year)
        FILTER(xsd:integer(?year) > 1780 && xsd:integer(?year) < 1981)# Any instance of a human.
            {?item wdt:P106 wd:Q11063}
            UNION
            {?item wdt:P101 wd:Q333} 
            UNION
            {?item wdt:P106 wd:Q169470}
            UNION
            {?item wdt:P101 wd:Q413}.

            ## inversed triple
			?s ?p ?item.
        }
		GROUP BY ?p
    }
    ?prop wikibase:directClaim ?p .

    ?prop rdfs:label ?propLabel.
        FILTER(LANG(?propLabel) = 'en')
    }  
ORDER BY DESC(?eff) 
 ```


## Exemple de requête concernant les appartenances à une organisation, avec dates optionnelles si connues


On doit dans cette requête sortir du cadre classique de la simple propriété 'member of' et passer à travers l'assertion, le *statement*. Un statement de _Wikidata_ apparait en quelques sortes comme une entité temporelle même si elle n'associe que deux entités principales, comme une propriété.

```
    SELECT DISTINCT ?item ?itemLabel ?birthYear ?statement ?organization ?organizationLabel 
                    ?startYear ?endYear  ?startTime ?endTime
    where {
            
        {?item wdt:P106 wd:Q11063}
                UNION
                {?item wdt:P101 wd:Q333}
            
        ?item wdt:P31 wd:Q5; # Any instance of a human.
                wdt:P569 ?birthDate;
                # member of
                p:P463 ?statement.
            ?statement ps:P463 ?organization.
        OPTIONAL {
                        ?statement pq:P580 ?startTime;
                        pq:P582 ?endTime.
            }
        
        BIND(REPLACE(str(?startTime), "(.*)([0-9]{4})(.*)", "$2") AS ?startYear)
        BIND(REPLACE(str(?endTime), "(.*)([0-9]{4})(.*)", "$2") AS ?endYear)
        
        BIND(REPLACE(str(?birthDate), "(.*)([0-9]{4})(.*)", "$2") AS ?birthYear)
        FILTER(xsd:integer(?birthYear) > 1700 && xsd:integer(?birthYear) < 1801)
            
        SERVICE wikibase:label { bd:serviceParam wikibase:language "en" }
        }
    ORDER BY ?birthYear ?startYear
```

### Autre exemple

```
     SELECT DISTINCT ?item ?itemLabel ?birthYear ?statement ?organization ?organizationLabel 
                    ?startYear ?endYear  ?startTime ?endTime
    where {
            
        {?item wdt:P106 wd:Q11063}
                UNION
                {?item wdt:P101 wd:Q333}
            
        ?item wdt:P31 wd:Q5; # Any instance of a human.
                wdt:P569 ?birthDate;
                # member of
                # p:P463 ?statement.
                # ?statement ps:P463 ?organization.
                # educated at
                p:P69 ?statement.
                ?statement ps:P69 ?organization.
              # employer
                #p:P108 ?statement.
                #?statement ps:P108 ?organization.
      #  OPTIONAL
      {
                        ?statement pq:P580 ?startTime;
                        pq:P582 ?endTime.
            }
        
        BIND(REPLACE(str(?startTime), "(.*)([0-9]{4})(.*)", "$2") AS ?startYear)
        BIND(REPLACE(str(?endTime), "(.*)([0-9]{4})(.*)", "$2") AS ?endYear)
        
        BIND(REPLACE(str(?birthDate), "(.*)([0-9]{4})(.*)", "$2") AS ?birthYear)
        FILTER(xsd:integer(?birthYear) > 1800 && xsd:integer(?birthYear) < 1901)
            
        SERVICE wikibase:label { bd:serviceParam wikibase:language "en" }
        }
    ORDER BY ?item
    
```