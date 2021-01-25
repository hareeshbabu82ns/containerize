# React App
- sample app to show containarizing React Web app
- serving through Nginx

## Docker build
```sh
$> docker build . -t registry.docker.local:5000/web/react-nginx:v0.0.1

# push to registry
$> docker push registry.unraid:33294/web/react-nginx:v0.0.1
```
