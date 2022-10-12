# Constitution d'un réseau marchable

Cette note traite de la constitution d'un réseau marchable destiné à des études d'accessibilité piétonne. Un réseau marchable comporte certaines spécificités:
- toutes les voies sont utilisables dans les deux sens
- certaines voies ne sont accessibles qu'à pied (passages à travers certains bâtiments, chemins non carrossables ou hors voirie)
- les vitesses de déplacement sont essentiellement constantes, de l'ordre de 4 - 5 kmh
- certaines voies sont inutilisables: voies rapides, autoroutes...
- lorsqu'un accès spécifiquement piéton est disponible (trottoir, traversée), il doit être préféré à la chaussée

## Evaluation des sources disponibles

### les réseaux de voirie de type ign ou de gestion

La donnée est de bonne qualité (exhaustivité, homogénéité) mais en raison de leur focus voirie, certaines lacune se font jour pour des cheminements spécifiquement piétons non dépendants d'une voirie (traversées de bâtiments, de parcs...)

### OSM

La nature non hiérarchisée d'OSM permet de l'enrichir avec des informations spécifiques à des thématiques parfois marginales. Ainsi les passages piétons (https://wiki.openstreetmap.org/wiki/FR:Key:crossing) ou les trottoirs (https://wiki.openstreetmap.org/wiki/FR:Tag:highway%3Dfootway).

Autre tag intéressant: https://wiki.openstreetmap.org/wiki/Relation:associatedStreet qui permet d'associer les objets trottoirs à la rue qu'ils bordent.

Inconvénient d'OSM découlant de sa nature participative: sa fiabilité générale, tant en termes d'exhaustivité que d'homogénéité, peut être questionnée. Aujourd'hui dans les grandes villes, les études considèrent OSM comme une source satisfaisante pour des travaux d'étude.

## Outils et traitements

### Extraire un réseau marchable

C'est une tâche consommatrice de ressources machine qui dépasse la simple écriture d'une requête (nécessité d'un algorithme logigramme traitant les relations).

Deux options sont possibles: extraction à partir d'une archive locale ou extraction directe sur les serveurs miroir d'osm. Le premier cas est plutôt destiné aux grands périmètres d'étude avec d'importants jeux de données, le deuxième plutôt aux extractiosn rapides sur de petites surfaces.

Dans notre cas, c'est la première solution qui sera choisie.

#### extraction de la donnée source

Téléchargement d'une archive `.pbf` sur https://geofabrik.de ou extraction d'un périmètre à façon sur https://bbbike.org

#### extraction du réseau

Elle se fait à l'aide de pyrosm (https://pyrosm.readthedocs.io/en/latest/) une librairie python basée sur geopandas, qui produit un geodataframe à partir d'une archive `.pbf`. Ce geodataframe peut ensuite être exportée dans une base postgres à l'aide des outils io de geopandas/sqlalchemy, ou bien être directement analysée sous forme de graphe en enchaînant NetworkX et OSMnx.

Dans notre cas, les traitements seront effectuées sous Grass à partir de données hébergées sous postgresql.

Dans un premier temps, télécharger une archive pbf.

```console
wget https://download.geofabrik.de/europe/france/rhone-alpes-latest.osm.pbf
```

Ensuite; extraire le réseau marchable et l'exporter dans postgres

```python

import requests
from pyrosm import get_data
from pyrosm import OSM
from sqlalchemy import create_engine

fp = get_data("rhone-alpes-latest.osm.pbf")

# Initialize the OSM parser object
osm = OSM(fp)

# Read all walkable roads
walk_net = osm.get_network(network_type="walking")

# export to postgres
engine = create_engine("postgresql://myusername:mypassword@myhost:5432/mydatabase")  
fp.to_postgis("my_table", engine)  
