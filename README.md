# hua-ops
Where backend meets frontend meets DevOps.

---

## Run with `docker-compose`

You can run the environment by deploying the services described in `docker-compose.yaml`. There are 4 services defined in this file. The additional **ingress** service manages the **api** and **ui** components to be deployed under the same host. 

__Important Note:__ This way of deploying the compnents is for testing purposes only and it is not fully functional. Certs errors are encountered with the addition of **ingress** component.  

Deploy the services in the following order:
1. db
2. api
3. ui
4. ingress
