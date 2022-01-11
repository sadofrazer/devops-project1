# PROJET FINAL DEVOPS. 

## **1- Introduction**

La société **IC GROUP** dans laquelle vous travaillez en tant qu’ingénieur Devops souhaite mettre sur pied un site web vitrine devant permettre d’accéder à ses 02 applications phares qui sont :  

1) Odoo et 
1) pgAdmin 

**Odoo**, un ERP multi usage qui permet de gérer les ventes, les achats, la comptabilité, l’inventaire, le personnel …  

Odoo est distribué en version communautaire et Enterprise. ICGROUP souhaite avoir la main sur le code et apporter ses propres modifications et customisations ainsi elle a opté pour l’édition communautaire.  Plusieurs versions de Odoo sont disponibles et celle retenue est la 13.0 car elle intègre un système de LMS (Learning Management System) qui sera utilisé pour publier les formations en internes et ainsi diffuser plus facilement l’information.  

Liens utiles : 

- Site officiel :[ https://www.odoo.com/ ](https://www.odoo.com/) 
- GitHub officiel:[ https://github.com/odoo/odoo.git ](https://github.com/odoo/odoo.git) 
- Docker Hub officiel :[ https://hub.docker.com/_/odoo ](https://hub.docker.com/_/odoo) 

**pgAdmin** quant à elle devra être utilisée pour administrer de façon graphique la base de données PostgreSQL crée précédemment. 

- Site officiel :[ https://www.pgadmin.org/ ](https://www.pgadmin.org/) 
- Docker Hub officiel:[ https://hub.docker.com/r/dpage/pgadmin4/ ](https://hub.docker.com/r/dpage/pgadmin4/) 

Le site web vitrine a été conçu par l’équipe de développeurs de l’entreprise et les fichiers y relatifs se trouvent dans le repo suscité : [ https://github.com/sadofrazer/ic-webapp.git ](https://github.com/sadofrazer/ic-webapp.git) . Il est de votre responsabilité de conteneuriser cette application tout en permettant la saisie des différentes URL des applications (Odoo et pgadmin) par le biais des variables d’environnement. 

Ci-dessous un aperçu du site vitrine attendu. 

![](images/site_vitrine.jpeg)

**NB :** L’image** créée devra permettre de lancer un container permettant d’héberger ce site web et ayant les liens adéquats permettant d’accéder à nos applications internes 


## **2- Conteneurisation de l’application web.** 

Il s’agit en effet d’une application web python utilisant le module Flask. L'image de base que nnous vous recommandons afin de conteneuriser cette application est :  `python:3.6-alpine`

Une fois le Dockerfile crée, Buildez le et lancer un container test permettant d’aller sur les sites web officiels de chacune de ces applications (site web officiels fournis ci-dessus). 

**Nom de l’image :**  ic-webapp ;*  
**tag :** 1.0*  
**container test_name :** test-ic-webapp* 

Une fois le test terminé, supprimez ce container test, testez les vulnérabilités de votre image à l'aide de l'outil `snyk`. Une fois terminé, poussez votre image sur votre registre Docker hub. 

## **3) Partie 1 : Déploiement des différentes applications dans un cluster Kubernetes.** 

### **a. Architecture** 

Les applications ou services seront déployées dans un cluster Minikube ou Kubeadm, donc à un ou plusieurs nœuds et devront respecter l’architecture suivante. 

![](images/synoptique_Kubernetes.jpeg)

En vous basant sur cette architecture logicielle, bien vouloir identifier en donnant le type et le rôle de chacune des ressources (A…H)  mentionnées dans cette architecture. 



### **b. Déploiement de l’application Odoo** 

Comme décrite ci-dessus, Odoo est une application web de type 2 tiers contenant différents modules facilitant la gestion administrative d’une société. 

En Vous servant des différents liens mentionnés ci-dessus, déployer Odoo à l’aide des images docker correspondantes et assurez vous que les données de la base de données Odoo soit persistantes et sauvegardées dans un répertoire de votre choix sur votre hôte. **NB**: respectez l’architecture ci-dessus 



### **c. Déploiement PgAdmin** 

Comme ci-dessus, servez-vous de la documentation de déploiement de PgAdmin sous forme de container afin de déployer votre application. 

Vous devez par la suite découvrir dans la documentation, le répertoire contenant les données et paramètres de l’application PgAdmin afin de le rendre persistant. 

Notez également que PgAdmin est une application web d’administration des bases de données PostgreSQL, Toutefois, le déploiement d’un container PgAdmin ne nécessite pas obligatoirement la fourniture des paramètres de connexion à une BDD, donc vous pouvez initialement déployer l’interface web en fournissant le minimum de paramètres requis (adresse mail + mot de passe) et ce n’est que par la suite par le biais de l’interface graphique que vous initierez les différentes connexion à vos bases de données. 

Afin de réduire le nombre de taches manuelles, nous souhaiterons qu’au démarrage de votre container PgAdmin, que ce dernier ait automatiquement les données nécessaires lui permettant de se connecter à votre BDD Odoo. Pour ce faire, il existe un fichier de configuration PgAdmin que vous devrez au préalable customiser et fournir par la suite à votre container sous forme de volume. 

Ce fichier doit être situé au niveau du container dans le répertoire : /pgadmin4/servers.json 

![](images/server_def.jpeg)


### **d. Déploiement des différentes applications** 

En vous servant des données ci-dessus, créez et testez les différents manifests correspondants aux ressources nécessaires au bon fonctionnement de l’application tout en respectant l’architecture fournie (Nbre de réplicas et persistance de données). 

Notez également que l’ensemble de ces ressources devront être crées dans un namespace particulier appelé «*icgroup*» et devront obligatoirement avoir toutes au moins le label « *env = prod* » 

**NB** : Etant donné que vos manifests pourront être publics (pousser vers un repo Git ), bien vouloir prendre les mesures nécessaires afin d’utiliser les ressources adéquates permettant de cacher vos informations sensibles. 


## **3- : Mise en place d'un pipeline CI/CD à l'aide de JENKINS et de ANSIBLE.** 
L'objectif de ICGROUP est en effet de mettre sur pied un pipeline CI/CD permettant l'intégration et le déploiement en continu de cette solution sur leurs différentes machines en environnement de test, puis de production  ( serveurs hébergés soit en On Premises soit dans le cloud AWS)

### **a. Pipeline Stages** 
![](images/pipeline.jpeg)

### **b. Infrastructure** 

Pour ce projet, on aura besoin de plusieurs serveurs hébergées soit dans le cloud ou en On Premises (VirtualBox, VMWare…) pour ceux qui n’ont pas de comptes cloud (AWS, AZURE ou autres).
Les serveurs nécessaires sont les suivants : docker_jenkins: [ https://github.com/sadofrazer/jenkins-frazer.git ](https://github.com/sadofrazer/jenkins-frazer.git)

1) **Serveur 1** : Jenkins (AWS, t2.medium, docker_jenkins: https://github.com/sadofrazer/jenkins-frazer.git)

**Environnement_test**: (NB à déployer automatiquement à l'aide de Terraform)
        1) **Serveur Admin** : Applications web site vitrine + pgadmin4 (AWS, t2.micro)
        2) **Serveur Odoo** : Application Odoo (AWS, t2.micro)

**Environnement_Prod**: (à déployer manuellement)
       Cluster kubernetes déployé avec soit minikube ou kubeadm [ Documentation kubeadm ](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/)
       si kubeadm (prévoir 01 master et 02 worker t2.medium)
       Si Minikube (prévoir juste une seule machine t2.medium)



### **c. Automatisation du déploiement**

Afin de faciliter le déploiement de nos applications à l'aide d'un pipeline CI/CD, nous vous conseillons de créer au préalables des rôles ansible que vous utiliserez pour vos déploiements (Pensez à variabiliser un maximum de paramètres et à créer autant de roles que vous jugerez nécesaires fonction des applications à déployer et également des différents environnements de déploiemlents : Docker et Kubernetes) 

**NB** : Ces rôles devront être appelés dans votre pipeline lors de la phase de déploiement avec les variabilisations qui vont bien.


### **d. Mise en place du pipeline**

Mettez sur pied votre pipeline CI/CD à l'aide de Jenkins qui permettra de déployer cette solution tout en déployant à l'aide de terraform les infrastructures de tests nécesaires. Concernant l'infrastructure de test qui sera déployée à l'aide de Terraform, pensez à utiliser la notion de remote backend afin d'éviter les conflits ou les doublons, et à détruire les précédentes infra avant d'en creer de nouvelles.

**NB** : N'oubliez pas de récupérer l'adresse ip publique de la machine de test qui sera crée par terraform afin de l'utiliser pour créer les Url ODOO et PGADMIN. 

### **e. Test de fonctionnement et rapport final**

Lancez l’exécution de votre pipeline manuellement pour une première fois, ensuite automatiquement après modification de votre fichier releases.txt (version : 1.1). Vérifiez que toutes les applis sont déployées et fonctionnent correctement. 


## 4- Rapports

A la fin du projet vous devez nous fournir :
- Une documentation de votre travail avec des captures d'écran
- Un Powerpoint pour présenter votre travail au public
- Toutes vos sources via un référentiel GitHub bien documenté

## 5- Have Fun

N'hésitez pas à ajouter des étapes supplémentaires, des outils supplémentaires pour rendre votre pipeline plus puissant (analyse de code, etc.)
Nous voulons que vous vous amusiez dans ce projet et exploriez les nouvelles possibilités offertes par le monde devops

## 6- Author
Ce projet a été créé par Frazer SADO, merci de ne pas le partager, le dupliquer...
Pour nous contacter : sadofrazer@yahoo.fr

LAST THING ...
![good luck](https://user-images.githubusercontent.com/18481009/84582398-cad38100-adeb-11ea-95e3-2a9d4c0d5437.gif)

 ## **7- ANNEXE** 

Ci-dessous un exemple de description des qualifications souhaitées pour un poste de Devops 

![](images/offre_emploi.jpeg)

**NB** : Bien vouloir preter attention aux qualités encadrées en jaune ci-dessus, vous vous rendez compte en effet que maitriser les technologies seulement ne suffit pas, il faut en plus de ca avoir un esprit très créatif, de très bonnes capacités redactionnelles pour rediger vos différents rapports et également des qualités de pédagogue qui vous aideront à parfaire les explications de vos actions dans vos différents rapports afin de faciliter leur compréhension. 

Compte tenu de tout cela, je vous invite tous à donner l’impotance à ce volet « rapport » de votre projet final, car c’est également une partie très importante qui devra pouvoir décrire le contenu de l’ensemble de votre travail.  

Merci de le rédiger correctement avec les captures d’écran, commentaires et explications qui vont bien car cette partie sera prise en compte dans votre note finale.
