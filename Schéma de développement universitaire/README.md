# Schéma de développement universitaire

Calcul d'un indicateur d'attractivité pour l'installation de résidences/pôles universitaires.

## Sources

Ce sont les attracteurs sur lesquels est basé le calcul de l'indicateur.

**Arrêts et lignes TC:**

Dans le cas d'infrastructures existantes, ce sont les arrêts qui sont pris en compte (points d'accès au réseau). Dans le cas d'infrastructures en projets, et si la position des arrêts d'est pas connue, ont prend en compte le tracé de la ligne.

Liste des infrastructures prises en compte: réseau Sytral (https://transport.data.gouv.fr/resources/8378?issue_type=DuplicateStops)
- Arrêts de métro
- Arrêts de tramway
- Sélection de lignes de bus C: C7, C20, C24, C6E, C1, C2, C3, C8
- infrastructures identifiées au plan de mandat Sytral: https://www.sytral.fr/610-plan-de-mandat-2021-2026.htm
- gares voyageurs SNCF (https://transport.data.gouv.fr/datasets/horaires-des-lignes-ter-sncf/)
- Pôles universitaires (donnée agence)

La donnée est structurée comme suit: la zone d'influence de l'infrastructure est modélisée par une zone tampon dont le rayon dépend du mode:
- 400 m pour les bus/trams
- 500 m pour les métros et les gares TER
- 1000 m pour les pôles universitaires

Ensute les zones tampons sont assemblées par ligne, formant des corridors.

**Centralités au sens du PLU**

Ce sont les zonages UCe du PLU du Grand Lyon

## Analyse spatiale

Toutes les zones sont superposées, puis découpées entre elles (union), et les superpositions sont comptées dans chaque polygone produit. L'indicateur recherché est le nombre de superpositions rapporté sur une échelle en 5 classes: de 0 à 5 et plus. Il est cartographié par une analyse thématique en dégradé de couleurs.


