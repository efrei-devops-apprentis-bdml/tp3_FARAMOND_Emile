# TP3 :smile:
## FARAMOND Emile

  

#### I) Consignes :

> Deployer sur Azure Container Instance (ACI) using Github Actions

> Mettre à disposition son image (format API) sur Azure Container Registry (ACR) using Github Actions

> Mettre à disposition son code dans un repository Github

  
> Lien aide : [https://docs.microsoft.com/en-us/azure/container-instances/container-instances-github-action](https://docs.microsoft.com/en-us/azure/container-instances/container-instances-github-action)

  

#### II) Etapes et mode d’emploi pour réaliser le TP :

1.  Rejoindre le github : efrei-devops-apprentis-bdml
    
2.  Se connecter au groupe de ressources devops-tp3 (sur Azure)
    
3.  Créer une github action (Actions> New WorkFlow > main.yml  :
\
    ![alt text](screenshots/1.png)

On spécifie  :

> la localisation : 
```bash 
location: 'france central'
```

> le dns : dns-name-label:
```bash
devops-20210245 (avec notre numero etudiant efrei)
```

> l’Azure Container Registry : 
```bash
efreidevops.azurecr.io
```

> l'ACI name : id efrei

> l'ACR repository name : id efrei


Remarque : Il faut ajouter dans les settings de sécurités sur github notre clé API openwheather puis on la récupere ici dans le main.yml avec : 
``` php
API_KEY=${{ secrets.API_KEY }}
```


4) Dans le  fichier de construction de application main.py , on specifie l'host et le port: 
```php
if __name__ == '__main__':
app.run(host = "0.0.0.0", debug=True,port=80)
```
> ATTENTION : Par défault, chez Azure le port est 80.

  

5)Ensuite, dans le fichier Dockerfile, on expose le port 80 
```php
EXPOSE 80
```
  
6) On verifie que le job soit bien crée :
\
 ![alt text](screenshots/2.png)

7) Sur azure, modifier “Default subscription filter” pour accepter “EFREI Apprentis-BDML”. Ensuite vérifier que le container ait bien était crée :
\
 ![alt text](screenshots/3.png)

puis qu’il expose sur le port 80  (dans les propriétés du conteneur) :
\
 ![alt text](screenshots/4.png)

8) Enfin, tester le curl avec la ligne de commande :
```bash
curl "http://devops-20210245.francecentral.azurecontainer.io/?lat=5.902785&lon=102.754175"
```
On obtient le resultat : 
\
 ![alt text](screenshots/5.png)
 
 ## Partie BONUS :
 
 Dans ce tp j'ai egalement réalisé la partie BONUS.
 
 Remarque: La partie sur liveness prob n'a pas fonctionée avec Azure.
 
 > Add hadolint au workflow
C'est la verification des "bonnes pratiques" sur docker.

Dans le fichier yaml on rajoute : 
```bash
uses: hadolint/hadolint-action@v2.0.0
with:
  dockerfile: Dockerfile
```


> Configuration d'une probe liveness HTTP

Il faut faire 2 choses : (i) ajouter un fichier yaml à la racine du projet, (ii) modifier le github action pour exécuter la modification (i).

##### (i) --> c'est le fichier liveness.yaml à la racine
##### (ii) --> on ajoute les lignes de congiguration suivante dans la github action : 
```bash
azcliversion: 2.30.0
inlineScript: |
  az container create --resource-group ${{ secrets.RESOURCE_GROUP }} --image ${{ secrets.REGISTRY_LOGIN_SERVER }}/20210245:v1 --dns-name-label devops-20210245 --registry-login-server ${{ secrets.REGISTRY_LOGIN_SERVER }} --registry-username ${{ secrets.REGISTRY_USERNAME }} --registry-password ${{ secrets.REGISTRY_PASSWORD }} --secure-environment-variables API_KEY=${{ secrets.API_KEY }} --location 'france central' --ports 80 -f liveness-probe.yaml
```

>> Remarque : Azure CI ne permet pas de passer des secrets tokens dans le repo github...

 
 ## Remarques sur le travail / Interets du tp: 
 
 Ce tp met en evidence différent point de github :
 
> La puissance de Github est mise en avant et son coté pratique pour le travail collaboratif grace au principe de versioning et branches.

> Le workflow peut facilement etre automatiser  grace auw Github Actions. La création, le test et le deploiement du code sont alors des étapes qui peuvent etre directement depuis github.

> Autre avantage : Github permet aux equipes de travailler en collabarotion (revues de code, gestion des branches et tri de problemes).
