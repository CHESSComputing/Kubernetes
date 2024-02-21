# FOXDEN kubernetes
This area contains all necessary files to manage FOXDEN on Kubernetes
infrastructure.


### Kubernetes setup
```
# create foxden namespace
kubectl create ns foxden

# create foxden secret
kubectl delete secrets -n foxden foxden-secrets
kubectl create secret generic foxden-secrets --from-file=k8secrets/.foxden.yaml --from-file=k8secrets/dbfile --dry-run=client -o yaml | kubectl apply --namespace=foxden -f -

# create foxden schemas secret
kubectl delete secrets -n foxden schema-secrets
kubectl create secret generic schema-secrets --from-file=k8secrets/schemas/test.json --from-file=k8secrets/schemas/ID1A3.json --from-file=k8secrets/schemas/ID3A.json --from-file=k8secrets/schemas/ID4B.json --dry-run=client -o yaml | kubectl apply --namespace=foxden -f -

# deploy services
kubectl apply -f Authz.yaml
kubectl apply -f DataBookkeeping.yaml
kubectl apply -f DataDiscovery.yaml
kubectl apply -f DataManagement.yaml
kubectl apply -f Frontend.yaml
kubectl apply -f MetaData.yaml
kubectl apply -f MLHub.yaml
kubectl apply -f PublicationService.yaml
kubectl apply -f SpecScansService.yaml

# delete deployments
kubectl delete deployment -n foxden authz
kubectl delete deployment -n foxden dbs
kubectl delete deployment -n foxden discovery
kubectl delete deployment -n foxden datamgt
kubectl delete deployment -n foxden frontend
kubectl delete deployment -n foxden metadata
kubectl delete deployment -n foxden mlhub
kubectl delete deployment -n foxden publish
kubectl delete deployment -n foxden scans
```
