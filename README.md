## BRIEF10 : DOCKER + AZURE = ❤️

groupe 5 : Alain, Driton, Yvette

création : 31/08/2022

liens sripts: https://github.com/Alain-Sardin/brief10/blob/main/brief10_creationR.sh




* * *

### Table des matières

> - Objectif
> - Préambule
> - Estimation initiale du temps de travail
> - Création des ressources Azure
> - Installation du wiki
> - Génération de l'image modifiée
> - Script de création ressources Azure
> - Processus d'installation amélioré
> - Problèmes rencontrés

* * *

## Objectif

Le boss en a marre que les mêmes questions reviennent sans cesse. Il s'est promis de mettre en place un outil interne de type "Stack Overflow" et de forcer les prochains questionneurs à l'utiliser.
Il ne sait pas quel outil utiliser, et il attend des propositions. L'utilisation de Docker est parfaite dans ce cas, car c'est rapide et interchangeable.

---

## Préambule
- Nous avons opté pour la mise en place d'un wiki. Cela permetra de documenter les sujets importants pour le Big Boss et d'avoir les réponses au questions courantes des employés.
- Pour réduire les coûts et travailler la communication, nous utilisons une base de donnée mutualisée MariaDB crée par Salem.
- Dans notre cas cela nous modifie la marche à suivre; nous n'utilisons plus de docker-compose pour generer 2 containers dont une db, mais la création d'un container unique.
- Comme documenté à la suite, l'installation sera faite en 2 étapes: 
    - une premiere étape de génération de la base de donnée et d'un fichier Local_settings.php à partir de l'image officielle mediawiki:stable à reintegrer à l'image pour cloturer la phase d'installation.
    - une deuxieme étape de géneration d'une image intégrant le fichier de settings et mise en ligne sur dockerhub pour qu'elle soit réutilisable lors de la création du site final.

* * *
## Estimation initiale du temps de travail
le 31/08/2022
temps total: 72h à 3 soit 24h/(8h jour) = 3 jours (voir avec les veilles)

- Determination des objectifs - 3h
- Chercher des infos - 9h
- Creation github perso/groupe - 3h
- Rechercher l'images docker hub adequate - 9h
    - stack overflow
    - db
- Creation ressource en GUI - 6h
    - app service plan
    - webapp
    - slot dev et prod
- Test image dockerhub - 9h
- Configuration image dockerhub - 6h
- Creation du script - 9h
- Debuging script - 9h
- Documentation en markdown - 6h
- Preparation démo orale - 3h



* * *
## Création des ressources Azure
    - Groupe de ressource
    - App service plan linux.
    - App service pour la webapp utilisant container docker avec l'image docker-hub mediawiki:stable
    - Ajout de deployment slot DEV au slot déjà existant de Production, utilisant une image alaincloud/mediawiki:dev3 differente pour les tests.

---
## Installation du wiki 
    - Depuis l'url du site nous suivons la procédure d'installation de mediawiki.
    - Renseignement des informations :
        - base de données : MariaDB , login, pass et host de salem ainsi que le nom de la database dediée dans le serveur MariaDB (my_wiki).
        - Compte administrateur du wiki
        - Selection des options de fonctionnement du wiki :
            - droit de consultation
            - droit de création d'article limité aux autorisés/ inscrits/ tout le monde.
            - nous laissons le wiki ouvert à tous en lecture et en creation pour la démo, mais pour une utilisation pro il faut plutôt limiter la création d'article aux autorisés inscrit, et la consultation aux inscrits.
        -

---

## Génération de l'image modifiée

---

## Script de création ressources Azure

---

## Processus d'installation amélioré

---



* * *
## Script de création ressources Azure
```bash
## Brief10 : Docker + Azure = love
## Script Done by : Yvette , Driton, Alain

###########################################################
## Script For Web APP Mediawiki - brief 10  Docker Azure ##
###########################################################

#variable preset
group=Groupe5_Brief10_YDA
location=westeurope
appserviceplanname=g5b10ydaasp
webappname=g5b10mediawiki


###########################################################
##Resource group
createRG(){
    echo " creating ressource groupe "
    az group create -n $group -l $location
}

############################################################
## create webapp adqsdq dqdq 
createApp(){
    echo " creating app plan "
    az appservice plan create -g $group  -n $appserviceplanname --is-linux --number-of-workers 4 --sku P1V2
    echo " creating Web app "
    az webapp create --resource-group $group --plan $appserviceplanname --name $webappname --deployment-container-image-name alaincloud/mediawiki:stable
    echo " creating slots"
    # reste à faire
    az webapp deployment slot create  --name $webappname --resource-group $group --slot DEV --deployment-container-image-name alaincloud/mediawiki:dev3 --configuration-source $webappname

    #echo " modif WP-config "
    #az webapp config appsettings set -n alainb8wap -g $group --settings MARIA_DB_HOST="alainb8-mdb.mariadb.database.azure.com" MARIA_DB_USER="$username"  MARIA_DB_PASSWORD="$password"  WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
}
###########################################################
#creation slot
# createSlot(){
#     az webapp deployment slot create  --name $webappname --resource-group $group --slot DEV --deployment-container-image-name alaincloud/mediawiki:dev3 --configuration-source $webappname
# }


############################################################
#tout creer
createAll(){
    createRG
    createApp
}

createAll
#createSlot
echo "installation terminée"
```



* * *

## Problèmes rencontrés
- difficultés à trouver une image adéquate.
- test d'une image askbot utilisant sqlite avant de changer
- avec l'image mediawiki :
    - Problème de génération de la base de donnée médiawiki , en particulier une requete basée sur l'engine MyISAM alors qu'Azure ne supporte que InnoDB.
        - Solution : création de la requete en remplaçant manuelement le storage engine par InnoDB directement dans la database. Puis actualisation de la page.
    - installation en deux étapes avec un fichier Local_settings.php à réintegrer à l'image
car l'edition du container sur azure impossible
    - image intégrant le fichier Local_settings.php ne permet pas la création des bases de données, donc nous avons crée la db avec la premiere image, et finisalisé le site avec la deuxieme.
