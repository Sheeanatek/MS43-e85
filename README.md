# MS43-e85


# Cartographie E85 pour les moteurs BMW M54.

## Tutoriel expliquant comment modifier un calculateur Siesmens MS43 équipant les moteurs BMW de type M54. ##

Ces modifications ont été faites sur ma BMW 325i E46 de 2002 qui me sert au quotidien.

### Pour cela il vous faut : 
* Une E46 equipée d'un M54 (320i, 325i ou 330i).
* A Pc portable.
* Si possible, il est conseillé d'effectuer ces modifications sur un deuxieme calculateur (on en trouve sur EBAY pour une cinquantaine d'euros, qu'importe la motorisation et le verouillage de l'antidemarrage).
* Un câble K-DCAN.


### Dans votre PC il vous faut :
* Les drivers du câble K-DCAN.
* INPA.
* Le logiciel MS4x flasher.
* Le logiciel Tuner Pro.
* Le fichier binaire original de 512k de votre moteur en version MS430069 (Par exemple M54B25 pour une 325i).
* Le fichier XDF de tuner pro pour le fichier binaire de 512k à la version MS430069.
* Le fichier XDF patchlist Tuner Pro pour le fichier binaire de 512k à la version MS430069.

Vous pouvez télécharger MS4X flasher, le fichier bin et les fichiers xdf sur [ms4x.net](https://www.ms4x.net/index.php?title=Main_Page)


## Avant les modifications ##

Il est recommandé de changer les bougies, le filtres à essence et si possible la pompe à essence avant de mettre de l'E85 dans votre reservoir.


## Modification des cartographies : ##

Toutes les valeurs indiquées sont approximatives et doivent être ajustées selon votre moteur (usure, étanchéité de l'admission...). Dans chaque sous-parties le nom de la table est associé à sa modification.


### Réchauffage du catalyseur ###

Vous pouvez désactiver ce réchauffage pour aider les demarrages à froid.
* c_conf_cat = 0.


### Tables d'injection (facile) : 

Avec la même quantité d'air, le calculateur doit injecter 30% d'E85 en plus par rapport à de l'E10 ou E5 pour obtenir une valeur lambda de 1. 
*(+30% veut dire qu'il faut appliquer une valeur multiplicative de 1.30 dans tuner pro.)*


**Charge partielle (partial load):**

Moteur froid (banc 1 et 2)
* ip_ti_tco_1_pl_1_ivvt_n_maf :  *+30%.*
* ip_ti_tco_1_pl_2_ivvt_n_maf :  *+30%.*

Moteur chaud (banc 1 et 2)
* ip_ti_tco_2_pl_1_ivvt_n_maf :  *+30%.*
* ip_ti_tco_2_pl_2_ivvt_n_maf :  *+30%.*


**Ralenti (idle):**

Moteur froid
* ip_ti_tco_1_is_ivvt_n_maf :  *+30%.*

Moteur chaud
* ip_ti_tco_2_is_ivvt_n_maf :  *+30%.*


**Démarrage (cranking) :**

La séquence de démarrage peut se diviser en trois parties :
* Pré injection à froid, je pense que c'est la première impulsion de carburant injectée en début de demarrage.
* Injection de demarrage, ce sont les tables utlisées durant la rotation du demarreur.
* Enrichissement après démarrage, permet une transition entre les tables de demarrage et de ralenti/charge partielle.
* A chaque démarrage à froid, soyez attentif au nombre de coup de demarreur, à la rapidité du demarrage et à la transition avec le ralenti, surtout sous une température de 10°C.


Pre injection time basic value (valeur de temps de base de pré-injection) : 
* ip_tipr_cst__tco : *+50% max en dessous de 10°C et des valeur multiplicative degressive de 30°C à 90°C . (Mon réglage : 30° > 48 ; 60° > 28 ; 90° > 19)*
* Attention, vous pouvez facilement noyer votre moteur au démarrage, faites plusieurs réglages progressifs. C'est la table la plus compliquée à affiner.


Cranking injection time basic value (valeur de temps de base d'injection durant le demarrage) :
* ip_ti_cst_n_tco : *+50% à 80rpm et 320rpm ensuite +35% pour 920rpm.*


Gradual reduction of fuel enrichment at engine start (réduction graduelle d'enrichissement de carburant au demarrage) : 
* ip_ti_cr_cst__cyc__tco : *Je n'ai pas encore modifié cette table.


Initialized value for post start enrichment factor (valeur initiale pour le facteur d'enrichissement après demarrage) : 
* ip_ti_cast__tia_tco : *+10% sous 30°C voire plus.*


Gradual reduction of fuel enrichment after engine start (réduction graduelle d'enrichissement de carburant après demarrage) : 
* ip_ti_cr_cast__cyc__tco : *Je n'ai pas encore modifié cette table.



### Allumage : 

L'E85 est moins sensible au cliquetis et a de plus grande capacité de refroidissement, un petit rajout d'avance est possible permettant un leger gain de couple. Dans ce calculateur, il y a 2 tables "principales" d'allumage, RON98 et RON91. Si le calculateur detecte du cliquetis, il bascule automatiquement de la table RON98 à RON91. Cette dernière a une courbe d'avance plus faible protègeant le moteur. Seule la table RON98 est à modifier.


**Charge pleine et partielle:**

Target ignition angle for RON98 during part and full load (angle d'allumage visé pour du RON98 à charge partielle et en pleine charge) : 
* ip_iga_ron_98_pl_ivvt_n_maf : *+11% , cela rajoute environ 4 degrès d'avance au maximum sur la table, plus peut être destructif pour le moteur !*



**Démarrage :**

Basing ignition angle at start (angle d'allumage de base au demarrage) : 
* ip_iga_st__n : *3° à 80rpm, 6° à 320rpm et 13.1° à 920.*



### Vanos : 

Intake camshaft setpoint during idle cold engine (angle de l'arbre à came d'admission au ralenti moteur froid) : 
* ip_cam_sp_tco_1_in_is__n__maf_iv : *Mettre 120° sur toute la table, cela permet d'éviter une ouverture simultanée des soupapes d'admission et d'echappement et facilite les démarrages à froid.*


### Régime de ralenti (idle speed) : 

Nominal idle speed without additionnal load on the engine (vitesse de ralenti nominal sans charge appliquée au moteur) : 
* ip_n_sp_is__tco : *Insérer 1120 @ -9.8° et 900 @ 9.8°. Cela aidera à maintenir le ralenti à la coupure du demarreur.*


### Facultatif : immo-off du calculateur : 

Ouvrez votre fichier binaire de 512k avec le fichier XDF de patch d'application et appliquer le patch de suppression de l'antidemarrage.


### Après les modifications :

Enregistrez votre fichier binaire 512k et chargez le dans le calculateur à l'aide du logiciel MS4X flasher.
Supprimez, sur MS4X flasher ou INPA, les valeurs d'adaptation des sondes lambda (important).


Testez votre nouvelle cartographie en vérifiant les valeurs des sondes lambda et surtout des valeurs de correction de richesse "lambda intégrator" sur INPA :
* Une valeur positive indique un melange trop pauvre et négative trop riche.
* Controlez ces valeurs au ralenti, en charge partielle et en pleine charge, moteur froid et chaud.
* Remodifiez les tables en fonction des valeurs "lambda intégrator".
* Si votre moteur à des prises d'air à l'admission comme ma 325i arrivée à 300 000km, vous pouvez enrichir certaines table pour compenser le temps de corriger ces entrées d'air.
* A chaque modification, ne pas oublier de réinitialiser les valeurs d'adaptation des sondes lambda.


### Sources : ##

[La bible ms4x.net](https://www.ms4x.net/index.php?title=Main_Page)

[Tuto intéressant sur E46 fanatics](https://www.e46fanatics.com/threads/m54-e-85-tuning-n-a-ms43.1263149/)
