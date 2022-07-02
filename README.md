# hua-ops
Where backend meets frontend meets DevOps.

## Project information

The frontend (ui) and backend (api) components are linked as submodules in this project. The solid way of deploying them is using Kubernetes. An alternative way of running and performing quick tests is to use the `docker-compose` option.

For the deployment we used `microk8s` with `kubectl` and `helm`.

## Run with `docker-compose`

You can run the environment by deploying the services described in `docker-compose.yaml`. There are 4 services defined in this file. The additional **ingress** service manages the **api** and **ui** components to be deployed under the same host. 

> __Important Note:__ This way of deploying the components is for testing purposes only and it is not fully functional. Certs errors are encountered with the addition of **ingress** component.  

Deploy the services in the following order:
1. db
2. api
3. ui
4. ingress

## Deploy with Kubernetes

The best approach is to create the cluster resources by applying the configuration from YAML files and creating secrets for credentials and other sensitive information. The necessary files are located to `k8s` folder.

## Improvements

*- How can this git project be updated automatically when a submodule is updated?*  
A feature like this can be implemented on the CI solution only if a Jenkinsfile is created and a new Pipeline is added for the current project. The idea is that a successful commit & build of either **ui** or **api** repo could trigger a job to update this git project.  

