# home-lab
homelab meets HA


## my goal
Create a High avaliable cluster for my homelab. This does mean it will extend past the home into my parents house...

# prereq

Create the cluster
Install argocd:
    kubectl create namespace argocd
    kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

Add repo to argocd
point argocd to the apps folder

# Install cert manager with cloudflare cluster issuer
kubectl apply -f ./infrastructure/base/argocd-apps/certmanager.yaml

## Install nginx in the cluster:
kubectl apply -f ./infrastructure/base/argocd-apps/nginx.yaml

## Install longhorn in the cluster:
kubectl apply -f ./infrastructure/base/argocd-apps/longhorn.yaml

#### allow longhorn privilledged:
kubectl label namespace longhorn-system pod-security.kubernetes.io/enforce=privileged
kubectl label namespace longhorn-system pod-security.kubernetes.io/enforce-version=latest


# Install Infisical and its operator for secert managment
kubectl apply -f ./infrastructure/base/argocd-apps/infisical.yaml

## add access to infisical from cluster
kubectl create secret -n infisical generic universal-auth-credentials --from-literal=clientId="<get from machine identities>" --from-literal=clientSecret="<get from machine identities>"

# install metallb
kubectl apply -f ./infrastructure/base/argocd-apps/metallb.yaml


#### allow metallb privilledged:
kubectl label namespace metallb pod-security.kubernetes.io/enforce=privileged
kubectl label namespace metallb pod-security.kubernetes.io/enforce-version=latest

# install pihole
kubectl apply -f ./infrastructure/base/argocd-apps/pihole.yaml

# TODO list
- Setup machines with talos
- Setup argocd to read this repo
- Setup Secert manager 
- test disaster recovery
- setup volume backups/snapshots
- test volume backups/snapshots
- how can i get multiple replicas working in pihole, one on each node?