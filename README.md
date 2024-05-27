# kind-cluster-01

kind create cluster --name k8s01 --config cluster01.yaml
kind delete cluster --name k8s01 


kubectl config get-contexts
kubectl config current-context

kubectl config use-context kubernetes-admin@kubernetes

## creat a docker network
docker network create --driver=bridge --subnet=172.16.100.0/24 --gateway=172.16.100.1 my-docker-ntw


kubectl config use-context kind-k8s01

## certify k8sAuthMethodHost
kubectl config view --raw --minify --flatten \ -o jsonpath='{.clusters[].clusters.server}'

## create consul-gossip

kubectl create secret generic consul-gossip-encryption-key --from-literal=key=$(consul keygen) -n consul
export CONSUL_GOSSIP=$(kubectl get --namespace consul secrets/consul-gossip-encryption-key --template={{.data.token}} | base64 -d)

## Get CA and bootsrap 
kubectl get secret consul-ca-cert consul-bootstrap-acl-token --output yaml > cluster1-credentials.yaml -n consul

## Get boostrap token
export CONSUL_HTTP_TOKEN=$(kubectl get --namespace consul secrets/consul-bootstrap-acl-token --template={{.data.token}} | base64 -d)

## license 
secret=$(cat consul.hclic)
kubectl create secret generic consul-ent-license --from-literal="key=${secret}" -n consul

## install 
helm install --values values.yaml consul hashicorp/consul --namespace consul --version "1.4.1"
helm upgrade --values consul/values.yaml consul hashicorp/consul --namespace consul --version "1.4.1"

kubectl create secret generic consul-bootstrap-acl-token --from-literal="token=15b5c9f8-27cb-34cb-189d-e2f1f1dc858f" -n consul 


## install 
helm install --values dataplane/dataplane.yaml consul hashicorp/consul --namespace consul --version "1.4.1"
helm upgrade --values dataplane/dataplane.yaml consul hashicorp/consul --namespace consul --version "1.4.1"

consul-bootstrap-acl-token          Opaque   1      17h
consul-ca-cert                      Opaque   1      17h
consul-consul-bootstrap-acl-token   Opaque   1      47s
consul-consul-ca-cert               Opaque   1      47s
consul-ent-license                  Opaque   1      6d22h