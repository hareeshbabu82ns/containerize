[![Build Status](https://drone.terabits.io/api/badges/hareeshbabu82ns/containerize/status.svg)](https://drone.terabits.io/hareeshbabu82ns/containerize)

# React App
- sample app to show containarizing React Web app
- serving through Nginx

## Docker build
```sh
$> docker build . -t registry.docker.local:5000/web/react-nginx:v0.0.1

# push to registry
$> docker push registry.unraid:33294/web/react-nginx:v0.0.1
```

## Service Account
* fetch the credentials of the Service Account to be configured on Drone
```sh
$> kubectl get serviceaccounts -A
$> kubectl get sa deploy-bot -o jsonpath='{.secrets[*].name}'
$> kubectl get secret deploy-bot-token-sn8jn -o jsonpath='{.data.ca\.crt}' && echo
$> kubectl get secret deploy-bot-token-sn8jn -o jsonpath='{.data.token}' | base64 --decode && echo
```
* __Note__: need a service account which has `Role` to create pods in the required namespace

## Drone CLI
* find the api token from user profile
```sh
$> export DRONE_SERVER=https://drone.terabits.io
$> export DRONE_TOKEN=XXXXXXXXX
$> drone info
```

* validate deployment from local
```sh
# to get the address
$> kubectl config view -o jsonpath='{range .clusters[*]}{.name}{"\t"}{.cluster.server}{"\n"}{end}'
$> kubectl config view -o jsonpath='{.clusters[?(@.name=="k3d-utmp")].cluster.server}'

$> export KUBERNETES_SERVER=https://rancher.uat.kube.terabits.io/k8s/clusters/local
$> export KUBERNETES_SERVER=https://192.168.86.74:46147
$> export KUBERNETES_CERT=
$> export KUBERNETES_TOKEN=

$> docker run --rm \
    -e PLUGIN_KUBERNETES_SERVER=$KUBERNETES_SERVER \
    -e PLUGIN_KUBERNETES_CERT=$KUBERNETES_CERT \
    -e PLUGIN_KUBERNETES_TOKEN=$KUBERNETES_TOKEN \
    -v $(pwd)/k8s:/data/k8s \
    sinlead/drone-kubectl apply -f /data/k8s/deployment-react-nginx.yaml

# to avoid 502 certificate error - not recomended
# --insecure-skip-tls-verify
docker run --rm \
    -e PLUGIN_KUBERNETES_SERVER=$KUBERNETES_SERVER \
    -e PLUGIN_KUBERNETES_CERT=$KUBERNETES_CERT \
    -e PLUGIN_KUBERNETES_TOKEN=$KUBERNETES_TOKEN \
    -v $(pwd)/k8s:/data/k8s \
    sinlead/drone-kubectl apply -f /data/k8s/deployment-react-nginx.yaml --insecure-skip-tls-verify
```

* setup up per repo secrets
```sh
$> drone secret add user/repo --name docker_user --data random-data-here
$> drone secret add user/repo --name docker_pass --data $FROM_ENV_VARIABLE
$> drone secret add user/repo --name k8s_server --data $KUBERNETES_SERVER
$> drone secret add user/repo --name k8s_cert --data $KUBERNETES_CERT
$> drone secret add user/repo --name k8s_token --data $KUBERNETES_TOKEN
```

* setting secrets using ansible
```sh
$> ansible-playbook site.yml --tags "drone-cli"
$> ansible-playbook site.yml --tags "drone-secrets"
$> ansible-playbook site.yml --tags "drone-secrets-local"
```