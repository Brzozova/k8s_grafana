# Grafana

## Overview

Guide how to deploy Grafana with PostgreSQL database and Azure AD integration.

## Create Postgresql database

1. Create database and user on database.
2. Set up database address, database name, username, password.

Filling everything is required for a new database. In case of an upgrade, just
update `password` in `grafana-passwords` Kubernetes secrets.

## SSL and Azure

!. Create CA
2. Paste cert and key to file, eg. `certificate.crt` and `certificate.key`
3. Create secret `grafana-tls-secret`. This step is required for Azure to work properly with SSL.

```bash
kubectl create secret tls grafana-tls-secret --key certificate.key --cert certificate.crt
```

### If Azure integration does not work properlyou 

1. Specify in values:

```bash
admin:
  userKey: admin-user
  passwordKey: admin-password
```
2. Change `auth.basic` to true and `auth` to false

```bash
grafana.ini:
[..]
  auth.basic :
    enabled : "true"
  auth :
    disable_login_form : "false"
```

## Secret

Database password and azure secret are stored in kubernetes secrets `grafana-passwords`. 
To create them we execute the command appropriately supplement it with passwords.

```bash
 kubectl create secret generic grafana-passwords --from-literal=password="XXXXXX" --from-literal=client_secret="YYYYYYY"
```

## Helm Repo

Helm repo used in this example -> [grafana helm repository](https://grafana.github.io/helm-charts).

## Deploy

```bash
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm upgrade --install grafana grafana/grafana \
             --values server_values.yaml
```
