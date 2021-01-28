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
$> kubectl get -n name-space secret deploy-token-xxxxx -o jsonpath='{.data.ca\.crt}' && echo
$> kubectl get -n name-space secret deploy-token-xxxxx -o jsonpath='{.data.token}' | base64 --decode && echo
```
* __Note__: need a service account which has `Role` to create pods in the required namespace
