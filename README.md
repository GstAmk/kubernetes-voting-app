# Voting app avec Kubernetes

**Mise en place d'une application distribuée composée de plusieurs microservices (frontend, backend, base de données, cache) conteneurisés et orchestrés par Kubernetes.**

## Architecture

<img src="img/diagram.png" alt="Diagramme de l'architecture du projet" width="350">

* **Vote App (Python)** : application web permettant aux utilisateurs de voter entre deux options (cat/dogs)
* **Redis** : cache qui mémorise les nouveaux votes
* **Worker (.NET)** : consomme les votes depuis Redis et les stocke dans une **base de données PostgreSQL** soutenue par un volume Docker
* **Result App (Node.js)** : application web qui affiche les résultats du vote en temps réel

## Application fonctionnelle sans Ingress

### Les différentes commandes à suivre
```
# Déploiement des microservices
kubectl apply -f db.yaml
kubectl apply -f redis.yaml
kubectl apply -f vote.yaml
kubectl apply -f worker.yaml
kubectl apply -f result.yaml

# Vérifier que tous les pods nécessaires sont en cours d'exécution
kubectl get pods

# Vérifier que les services sont exposés sur les ports adéquats
kubectl get svc
```
Suivant le résultat de `kubectl get svc`, vous devriez pouvoir accéder aux applications suivantes depuis les ports exposés sur votre machine locale (nodePort définis dans **vote.yml** et **result.yml**) :

* Vote App : [http://localhost:31000](http://localhost:31000)
* Result App : [http://localhost:31001](http://localhost:31001)

### Procédure à suivre en cas de problèmes

Dans le cas où vous n'arriveriez pas à accéder à vos applications, veuillez suivre ces instructions :

1. Exécutez `kubectl get endpoints` et assurez-vous que chaque service possède bien un ENDPOINT.
Si l'un des services affiche `<none>` en tant qu'ENDPOINT, passez à l'étape suivante.
2. Exécutez `kubectl get pods` et vérifiez que vous ne possédez pas plusieurs pods pour un même service. 
Si c'est le cas, il peut s'agir d'un problème de replicas.
Vous devrez alors exécuter les commandes suivantes :

```
# Supprimer le déploiement problématique
kubectl delete deployment <nom-deployment>

# Réappliquer le déploiement
kubectl apply -f <fichier.yml>

# Vérifier que le service récupère bien un ENDPOINT
kubectl get endpoints <nom-service>
```
