

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
SELECT (COUNT(*) as ?eff)
WHERE {
    SELECT DISTINCT ?item
    WHERE {
  ?item wdt:P31 wd:Q5 ;
        p:P39 ?poste.
  ?poste ps:P39 wd:Q3044918 ;
         pq:P580 ?starttime.
  FILTER(YEAR(?starttime) >= 1958)
}
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



### Nombre de députés de la 5e République par tranche de décennies

```
SELECT ?decennie (COUNT(DISTINCT?item)AS ?eff)
WHERE {
  ?item wdt:P31 wd:Q5 ;
        p:P39 ?poste.
 
  ?poste ps:P39 wd:Q3044918 ;
         pq:P580 ?starttime.
  FILTER(YEAR(?starttime) >= 1958)
#FLOOR arrondit à la décennie inférieure
#ex:1967 FLOOR(1967/10)*10)
BIND(CONCAT(STR(FLOOR(YEAR(?starttime)/10)*10), "s")AS ?decennie)
}
     GROUP BY ?decennie
     ORDER BY ?decennie
 ```
1950s	388

1960s	632

1970s	587

1980s	768

1990s	807

2000s	747

2010s	1130

2020s	855

Certains députés sont présents d'une décennie sur l'autre ce qui augmente l'effectif total

### Nombre de députés par présidents

```
SELECT ?tranche (COUNT(DISTINCT ?item) AS ?eff)
WHERE {
    ?item wdt:P31 wd:Q5 ;
        p:P39 ?poste.
  ?poste ps:P39 wd:Q3044918 ;
         pq:P580 ?starttime.
  FILTER(YEAR(?starttime) >= 1958)

#On crée une variable ?tranche selon l'année
BIND(
    IF(YEAR(?starttime)<1970,'1958-1969',
    IF(YEAR(?starttime)<1975,'1969-1974',
    IF(YEAR(?starttime)<1982,'1974-1981',
    IF(YEAR(?starttime)<1996,'1981-1995',
    IF(YEAR(?starttime)<2008,'1995-2007',
    IF(YEAR(?starttime)<2013,'2007-2012',
    IF(YEAR(?starttime)<2018,'2012-2017',
    IF(YEAR(?starttime)<2027,'2017-2027', '2027+'))))))))
    AS ?tranche)

}
GROUP BY ?tranche
ORDER BY ?tranche 
```
#
	            ?tranche	 ?eff
1	 Charles De Gaulle 1958-1969	: 826

2	Georges Pompidou 1969-1974	: 401

3	Valéry Giscard D'Estaing 1974-1981	: 583

4	François Mitterand 1981-1995	: 930

5	Jacques Chirac 1995-2007	: 934

6	Nicolas Sarkozy 2007-2012	: 649

7	François Hollande 2012-2017	: 649

Emmanuel Macron	2017-2027	: 892
### Persons filtered by birth year

Par exemple ici les députés de la 5e République


```
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
SELECT DISTINCT ?item ?itemLabel ?year
        WHERE {
        ?item wdt:P31 wd:Q5; 
              wdt:P569 ?birthDate.
        BIND(REPLACE(str(?birthDate), "(.*)([0-9]{4})(.*)", "$2") AS ?year)
        FILTER(xsd:integer(?year) > 1870)# Any instance of a human.
           ?item wdt:P31 wd:Q5 ;
        p:P39 ?poste.
  ?poste ps:P39 wd:Q3044918 ;
         pq:P580 ?starttime.
  FILTER(YEAR(?starttime) >= 1958)
            }
?item rdfs:label ?itemLabel.
  FILTER (LANG(?itemLabel) = "fr") .
ORDER BY ?item ?itemlabel 
```
NB: il peut y avoir des doublons si les dates de naissance sont multiples. La clause DISTINCT permet d'enlever les doublons il faut toutefois enlever la variable *?birthDate* de la sortie et laisser seulement l'année




## Lister les propriétés disponibles avec effectifs


!!! Les effectifs des propriétés sont multipliés par rapport aux nombres d'individus

### Sortantes

Cf. [sur cette page](./Wikidata-liste-proprietes-population.md) les listes de propiétés qui résultent de cette requête
```
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX wikibase: <http://wikiba.se/ontology#>
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
SELECT ?p ?propLabel ?eff ('[note]' as ?notes)
WHERE {
{
    SELECT DISTINCT  ?p  (COUNT(*) as ?eff)
    WHERE {
         ?item wdt:P31 wd:Q5 ;
        p:P39 ?poste.
  ?poste ps:P39 wd:Q3044918 ;
         pq:P580 ?starttime.
  FILTER(YEAR(?starttime) >= 1958)
			?item ?p ?o.
        }
		GROUP BY ?p
    }
    ?prop wikibase:directClaim ?p .

    ?prop rdfs:label ?propLabel.
        FILTER(LANG(?propLabel) = 'fr')
    }  
ORDER BY DESC(?eff)  
```

NB Noter qu'il peut y avoir des problèmes de time-out, la requête est trop longue et on a un message d'erreur.
<br/>
Dans ce cas il faut restreindre la période ou limiter le nombre de clauses UNION et décomposer la requête en différentes parties.

Nombre de propriétés différents au sein de l'effectif relevé au 2 mars 2026 : 765

This list is then exported and transformed to a table in order to document the sequence of operations. This is done as follows:

* execute the query then download the result in csv format into your projet's repository. Cf. the file in this directory: data_queries/Wikidata/wdt_population_outgoing_properties_20260302.csv
* open the CSV in VSCode as text and convert it to a Markdown Table using the plugin 'CSV to Markdown Table (phoihos)'
* copy the whole table and paste it a new Markdown document, cf. [Wikidata-liste-proprietes-population.md](Wikidata-liste-proprietes-population.md)
* close the CSV file

In the column 'notes' of the property table you can add links to the pages where you document the treatement of the corresponding information.   





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
        ?item wdt:P31 wd:Q5 ;
        p:P39 ?poste.
 
  ?poste ps:P39 wd:Q3044918 ;
         pq:P580 ?starttime.
  FILTER(YEAR(?starttime) >= 1958)
			?item ?p ?o.
        }
		GROUP BY ?p ?itemType
        ## limit to more frequent properties
		HAVING(?eff >= 100)
    }
    ?prop wikibase:directClaim ?p .

    ?prop rdfs:label ?propLabel.
        FILTER(LANG(?propLabel) = 'fr')
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
        ?item wdt:P31 wd:Q5 ;
        p:P39 ?poste.
  ?poste ps:P39 wd:Q3044918 ;
         pq:P580 ?starttime.
  FILTER(YEAR(?starttime) >= 1958)
			?item ?p ?o.
        }
		GROUP BY ?p ?itemType
		HAVING(?eff >= 100)
    }
    ?prop wikibase:directClaim ?p .

    ?prop rdfs:label ?propLabel.
        FILTER(LANG(?propLabel) = 'fr')
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
         ?item wdt:P31 wd:Q5 ;
        p:P39 ?poste.
  ?poste ps:P39 wd:Q3044918 ;
         pq:P580 ?starttime.
  FILTER(YEAR(?starttime) >= 1958)

            ## inversed triple
			?s ?p ?item.
        }
		GROUP BY ?p
    }
    ?prop wikibase:directClaim ?p .

    ?prop rdfs:label ?propLabel.
        FILTER(LANG(?propLabel) = 'fr')
    }  
ORDER BY DESC(?eff) 
 ```


## Exemple de requête concernant les appartenances à une organisation, avec dates optionnelles si connues


On doit dans cette requête sortir du cadre classique de la simple propriété 'member of' et passer à travers l'assertion, le *statement*. Un statement de _Wikidata_ apparait en quelques sortes comme une entité temporelle même si elle n'associe que deux entités principales, comme une propriété.

```
    SELECT DISTINCT ?item ?itemLabel ?birthYear ?statement ?organization ?organizationLabel 
                    ?startYear ?endYear  ?startTime ?endTime
    where {
            
         {?item wdt:P31 wd:Q5 ;
        p:P39 ?poste.
  ?poste ps:P39 wd:Q3044918 ;
         pq:P580 ?starttime.
  FILTER(YEAR(?starttime) >= 1958)}
            
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
        FILTER(xsd:integer(?birthYear) > 1800)
            
        ?item rdfs:label ?itemLabel.
    FILTER(LANG(?itemLabel) = 'fr')
        }
    ORDER BY ?birthYear ?startYear
```
### Exemple en se concentrant sur les fonctions assumées par les individus


```
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX wikibase: <http://wikiba.se/ontology#>
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX p: <http://www.wikidata.org/prop/>
PREFIX ps: <http://www.wikidata.org/prop/statement/>
PREFIX pq: <http://www.wikidata.org/prop/qualifier/>

PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
SELECT ?organization ?organizationLabel (COUNT (DISTINCT ?item) AS ?eff)
WHERE {
SELECT DISTINCT ?item ?itemLabel ?birthYear ?statement ?organization ?organizationLabel 
                    ?startYear ?endYear  ?startTime ?endTime
    where {
            
         {?item wdt:P31 wd:Q5 ;
        p:P39 ?poste.
  ?poste ps:P39 wd:Q3044918 ;
         pq:P580 ?starttime.
  FILTER(YEAR(?starttime) >= 1958)}
            
        ?item wdt:P31 wd:Q5; # Any instance of a human.
                wdt:P569 ?birthDate;
                # member of
                p:P39 ?statement.
            ?statement ps:P39 ?organization.
        OPTIONAL {
                        ?statement pq:P580 ?startTime;
                        pq:P582 ?endTime.
            
			}
        
        BIND(REPLACE(str(?startTime), "(.*)([0-9]{4})(.*)", "$2") AS ?startYear)
        BIND(REPLACE(str(?endTime), "(.*)([0-9]{4})(.*)", "$2") AS ?endYear)
        
        BIND(REPLACE(str(?birthDate), "(.*)([0-9]{4})(.*)", "$2") AS ?birthYear)
        FILTER(xsd:integer(?birthYear) > 1800)
            
         ?item rdfs:label ?itemLabel.
    FILTER(LANG(?itemLabel) = 'fr')
	}
        }
GROUP BY ?organization ?organizationLabel 
ORDER BY DESC (?eff) 
```

### Autre exemple

```
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX wikibase: <http://wikiba.se/ontology#>
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX p: <http://www.wikidata.org/prop/>
PREFIX ps: <http://www.wikidata.org/prop/statement/>
PREFIX pq: <http://www.wikidata.org/prop/qualifier/>

PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
SELECT DISTINCT ?item ?itemLabel ?birthYear ?statement ?organization ?organizationLabel 
                    ?startYear ?endYear  ?starttime ?endtime
    where {
            
         {?item wdt:P31 wd:Q5 ;
        p:P39 ?poste.
  ?poste ps:P39 wd:Q3044918 ;
         pq:P580 ?starttime.
  FILTER(YEAR(?starttime) >= 1958)
          }
            
        ?item wdt:P31 wd:Q5; # Any instance of a human.
                wdt:P569 ?birthDate;
                # member of
                # p:P39 ?statement.
                # ?statement ps:P39 ?organization.
                # educated at
                p:P69 ?statement.
                ?statement ps:P69 ?organization.
              # employer
                #p:P108 ?statement.
                #?statement ps:P108 ?organization.
     
        BIND(REPLACE(str(?starttime), "(.*)([0-9]{4})(.*)", "$2") AS ?startYear)
        BIND(REPLACE(str(?endtime), "(.*)([0-9]{4})(.*)", "$2") AS ?endYear)
        
        BIND(REPLACE(str(?birthDate), "(.*)([0-9]{4})(.*)", "$2") AS ?birthYear)
        FILTER(xsd:integer(?birthYear) > 1800)
            
        ?item rdfs:label ?itemLabel.
    FILTER(LANG(?itemLabel) = 'fr')
        }
    ORDER BY ?item
    
```
Les résultats peuvent ensuite être expolités sous la forme de fichier csv

### Nombre d'appartenance à une organisation par un député classé par ordre croissant
```
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX wikibase: <http://wikiba.se/ontology#>
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX p: <http://www.wikidata.org/prop/>
PREFIX ps: <http://www.wikidata.org/prop/statement/>
PREFIX pq: <http://www.wikidata.org/prop/qualifier/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
SELECT ?item ?itemLabel (COUNT ( ?organization) as ?eff)
WHERE {
SELECT DISTINCT ?item ?itemLabel ?birthYear ?statement ?organization ?organizationLabel ?startYear ?endYear ?starttime
WHERE {
  {
    ?item wdt:P31 wd:Q5 ;
          p:P39 ?poste .
    ?poste ps:P39 wd:Q3044918 ;
           pq:P580 ?starttime .
    FILTER (YEAR(?starttime) >= 1958)
  }
  ?item wdt:P31 wd:Q5 ; # Any instance of a human.
        wdt:P569 ?birthDate ;
  # member of
  # p:P39 ?statement.
  # ?statement ps:P39 ?organization.
  # educated at
        p:P69 ?statement .
  ?statement ps:P69 ?organization .
  # employer
  #p:P108 ?statement.
  #?statement ps:P108 ?organization.
  BIND (REPLACE(STR(?starttime), "(.*)([0-9]{4})(.*)", "$2") AS ?startYear)
  BIND (REPLACE(STR(?endtime), "(.*)([0-9]{4})(.*)", "$2") AS ?endYear)
  BIND (REPLACE(STR(?birthDate), "(.*)([0-9]{4})(.*)", "$2") AS ?birthYear)
  FILTER (xsd:integer(?birthYear) > 1800)
  ?item rdfs:label ?itemLabel .
  FILTER (LANG(?itemLabel) = 'fr')
}
}
GROUP BY ?item ?itemLabel
ORDER BY DESC (?eff)
```
