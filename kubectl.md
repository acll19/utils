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
<!-- List of pod events with specific reason -->
```bash
kubectl get events \
    --field-selector type=Warning,reason=BackOff,involvedObject=Pod \
    --all-namespaces
```