# FOXDEN kubernetes
This area contains all necessary files to manage FOXDEN on Kubernetes
infrastructure.

### Setup kubernetes cluster on macOS (M2, arm64)
- install docker desktop
- visit docker desktop settings and enable Kuberneres
- install `kubectl` and configure it to use docker desktop
```
docker context ls
kubectl config use-context docker-desktop
kubectl describe node docker-desktop
```
- setup nginx ingress

```
# please use recent version from
# https://github.com/kubernetes/ingress-nginx
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.10.0/deploy/static/provider/cloud/deploy.yaml
```
- the docker desktop URL and IP can be found via:
```
kubectl cluster-info
```


### Kubernetes setup
Please create `k8secrets` directory and store over there `dbfile`
and Metadata schemas, e.g.
```
tree k8secrets
k8secrets
├── dbfile
└── schemas
    ├── ID1A3.json
    ├── ID3A.json
    ├── ID4B.json
    ├── common.json
    ├── lite.json
    ├── mysql.sql
    ├── sqlite.sql
    └── test.json

2 directories, 9 files
```

Next, setup your k8s cluster and populate it with the following:
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

And, now we can access our service via:
```
curl -v http://kubernetes.docker.internal/apis
```

### References
- https://docs.docker.com/manuals/
- https://kubernetes.github.io/ingress-nginx/deploy/
