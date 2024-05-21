# GKE Gateway Demo: Blue Green Deployment

# Overview

![](gke-gateway-api-demo-04.png)

GatewayClass "gke-l7-global-external-managed" will deploy a Global external Application Load Balancer.

# Steps

## Step 1: deploy Self managed certificate.

[Create a private key and certificate](https://cloud.google.com/load-balancing/docs/ssl-certificates/self-managed-certs#create-key-and-cert) Then upload the certificate to certificate manager :

```bash
❯ gcloud certificate-manager certificates create store-example-com-cert \
    --certificate-file="CERTIFICATE_FILE" \
    --private-key-file="PRIVATE_KEY_FILE"
```

Create a certificate Map :

```bash
❯ gcloud certificate-manager maps create gke-poc-map
```

Create a certificate map entry:

```bash
❯ gcloud certificate-manager maps entries create store-example-com-map-entry \
    --map="gke-poc-map" \
    --certificates="store-example-com-cert" \
    --hostname="store.example.com"
```

## Step 2: Create infra namespace

```bash
❯ kubectl create namespace infra
```

## Step 3: Deploy the gateway

```bash
❯ kubectl apply -f gateway.yaml
```

## Step 4: Create web and api deployments & services

```bash
❯ kubectl apply -f web.yaml
```

## Step 5: Invoking the gateway

Go to GCP > network Services > Load balancing, get the IP of the load balancer and submit a http get requests

```
response 404 (backend NotFound), service rules for the path non-existent
```

## Step 6: create http routes

```bash
kubectl apply -f route.yaml
```

## Step 7: Invoke the gateway

```bash
❯ while true; do curl -ks https://store.example.com/ --resolve store.example.com:443:xx.xx.xx.xx | jq .metadata; sleep 1; done
```

```bash
"web-v1"
"web-v1"
"web-v1"
"web-v1"
"web-v1"
"web-v1"
"web-v1"
"web-v1"
"web-v1"
"web-v2"
"web-v1"
"web-v1"
"web-v1"
...
```
