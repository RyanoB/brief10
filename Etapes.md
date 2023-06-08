# Brief10

## Objectif

Mettre en oeuvre une solution de monitoring complète pour l’application Azure Vote App

- Déployer un service de collecte des logs pour les containers Kubernetes
- Un service de collecte des metrics des containers 
- Un service d’affichage des données collecté 

Outils : Grafana, Loki et Prometheus.

## Etapes

### Déployer un Cluster AKS 

- Connexion azure CLI **az login**

- Création d'un groupe de ressource **az group create --name rgry --location westeurope**

- Création du cluster AKS avec 1 nodes et la clé SSH de l'utilisateur dans le groupe de ressource **az aks create -g rgry -n AKSCluster --node-count 1 --ssh-key-value ./.ssh/id_rsa.pub --enable-managed-identity -a ingress-appgw --appgw-name Gateway --appgw-subnet-cidr "10.225.0.0/16"**

- Téléchargement des informations d’identification et configuration de l’interface de ligne de commande Kubernetes **az aks get-credentials --resource-group rgry --name AKSCluster**

- Problème rencontré avec le mdp REDIS **kubectl create secret generic redis --from-literal="REDIS_PWD="**


### Installation des outils

- Install grafana ****helm install grafana grafana/grafana --namespace monitoring --create-namespace

- Install loki ****helm install loki grafana/loki-stack -n monitoring
- Intsall prometheus ****helm install prometheus prometheus-community/prometheus

- Ajout du repo ****helm repo add grafana https://grafana.github.io/helm-charts/n
- Ajout du repo ****helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

- Reset password grafana ****kubectl exec -it stable-grafana-7b5c584454-7rdsr -c grafana -- grafana-cli admin reset-admin-password testtesttest

### Une fois sur Grafana

- Connecter Loki et Promethe à Grafana
P = http://prometheus.server.monitoring.svc.cluster.local
L = hhtp://loki.monitoring.svc.cluster.local:3100

Dashboard > administration > plugin > redis > install

- Créer les alertes
Menu > Alerting > Create Alerte rule

- Configuration des alertes
Exemple : Metric mémoire consommé par Redis
Expression : $A / $B * 100 pour avoir le pourcentage
Thresold condition envoie une alerte > Redis memory 70
label : Pour savoir a qui envoyer l'alerte
