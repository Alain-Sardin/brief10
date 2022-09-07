## BRIEF10 : DOCKER + AZURE = ❤️

création : 31/08/2022
groupe 5 : Alain, Driton, Yvette

liens sripts: https://github.com/Alain-Sardin/brief10/blob/main/brief10_creationR.sh




* * *

### Table des matières

> - Objectif
> - Estimation initiale
> - Préambule
> - Créer un groupe de ressources
> - Créer une passerelle NAT
> - Céation Azure Database MariaDB (saas)
> - Script de création ressources Azure
> - Problèmes rencontrés

* * *

## Objectif

Le boss en a marre que les mêmes questions reviennent sans cesse. Il s'est promis de mettre en place un outil interne de type "Stack Overflow" et de forcer les prochains questionneurs à l'utiliser.
Il ne sait pas quel outil utiliser, et il attend des propositions. L'utilisation de Docker est parfaite dans ce cas, car c'est rapide et interchangeable.


* * *
## Estimation initiale

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

## Préambule



* * *
## Script de création ressources Azure
```bash
## Brief10 : Docker + Azure = love
## Script Done by : Yvette , Driton, Alain

###########################################################
## Script For Web APP Wordpress - autoscaled - + MariaDB ##
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
    - installation en deux étapes avec un fichier Local_settings.php à réintegrer à l'image
car l'edition du container sur azure impossible
    - image intégrant le fichier Local_settings.php ne permet pas la création des bases de données, donc nous avons crée la db avec la premiere image, et finisalisé le site avec la deuxieme.
