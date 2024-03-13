# Useful kubectl commands

## Kubectl quick reference

https://kubernetes.io/docs/reference/kubectl/quick-reference/


## Test network

```bash

# ping
kubectl run --restart=Never --rm -i --tty network-multitool \
    --image=praqma/network-multitool -- ping <POD OR SVC IP>

# nslookup
kubectl run --restart=Never --rm -i --tty busybox \
    --image=busybox \
    --command nslookup <DOMAIN>
```

## Connect to a Postgres DB from K8s

```bash
kubectl run --restart=Never --rm -i --tty pgclient \
    --image=jbergknoff/postgresql-client \
    --env="POSTGRES_USER=<USER NAME>" \
    --env="POSTGRES_PASSWORD=<PASSWORD>" \
    --env="POSGRES_HOST=<HOST>" \
    --env="POSTGRES_DATABASE=<DB NAME>" \
    --env="POSTGRES_PORT=5432" \
    --command /bin/sh

# then...
PGPASSWORD=$POSTGRES_PASSWORD psql -U $POSTGRES_USER -h $POSTGRES_HOST -d $POSTGRES_DATABASE

# useful psql commands

# list DBs
\l

# set output to file
\o /path/to/file.txt
```

## Debugging

```bash
# List of pod events with specific reason
kubectl get events \
    --field-selector type=Warning,reason=BackOff,involvedObject=Pod \
    --all-namespaces

# List all names of a resource with a specific annotation/value filter
QUERY='.items[].metadata|select(.annotations."<ANNOTATION>"!=null)|.name'
kubectl get <RESOURCE> -ojson | jq $QUERY

# List all names of a resource with a specific label/value filter
QUERY='.items[].metadata|select(.labels."<ANNOTATION>"!=null)|.name'
kubectl get <RESOURCE> -ojson | jq $QUERY

# List all pods whose service acccount is the default
kubeclt get pods \
    --all-namespaces \
    -o jsonpath='{range .items[?(@.spec.serviceAccountName == "default")]}{.metadata.namespace} {.metadata.name}{"\n"}{end}' 2>/dev/null

# List number of pods in a cluster
kubectl get pods \
    --all-namespaces \
    --field-selector=status.phase!=Succeeded,status.phase!=Failed \
    --output json | jq -j '.items | length'
```