# Deploy with Kubernetes

The functional components to be deployed are the following:
- db (PostgreSQL)
- api (Java/Spring)
- ui (JS/Angular)

Many other components are required to orchestrate the deployment, like Secrets, Services, ConfigMaps, Ingress controllers etc.

In following paragraphs is described the complete deployment procedure.

## Database (db)

A **PostgreSQL** database is used to persist app data. We installed it to the k8s cluster using `helm`. A customized `values.yaml` file is passed in the install command to define the parameters for user, password and database name.

This is the command to install the database component:
```bash
helm3 install postgres -f values.yaml bitnami/postgresql
```

We also created 2 secrets: one for storing the DB connection credentials and one containing a SQL file to use and initialize the tables:
```bash
# create secret for credentials
kubectl create secret generic hua-secret --from-literal=db_user='mydbuser' --from-literal=db_password='mydbpass'
# create secret for db init sql script
kubectl create secret generic hua-secret-sql --from-file=path/to/init.sql
```
> **Note:** The database is not initialized automatically on creation. A manual step needs to be performed and run the init SQL script `hua-secret-sql`.


## Backend (api)

The backend component is a Java/Spring application. The built files are available at GitHub packages (ghcr.io). 

A ConfigMap named `api-cm` is created to store the properties defined in `api.env` file. 

The `api-deployment.yaml` is the deployment file. Other resources are being used by this deployment, like:
- the previously created Secret `hua-secret` for database credentials
- the previously created Secret `hua-secret-sql` to pass the database initialization script in the form of a mounted Volume.
- a Secret named `dockerconfigjson-github-com` which contains the GitHub username/API key information. Without it the **api** component's image could not be pulled from GitHub packages.

The application is exposed on port 8080.

The `api-clip.yaml` is the ClusterIP Service file. 

Once the mentioned Secrets are created, apply the api-* YAML files by running:
```bash
kubectl apply -f api-<name>.yaml
```

> **Note:** Detailed information for the creation of Secret `dockerconfigjson-github-com` can be found at the official [GitHub documentation](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry).


## Frontend (ui)

The frontend component is an Angular web application. It is deployed on a NGINX web server.  
Deploying the standalone component is simple, running `kubectl apply -f ui-deployment.yaml` will create a Deployment running the image which is publicly available on DockerHub.  
The application is deployed on container port 80. 

To completely deploy our application and enable it to handle HTTPS requests, further configuration is needed:

- File `ui-clip.yaml` defines a LoadBalancer (Service) for the frontend application.
- File `ui-ingress-ssl.yaml` defines an Ingress resource.  
It enables HTTPS connections by configuring SSL/TLS on production host [huademo.gotdns.ch](https://huademo.gotdns.ch).  
It also serves as the main Ingress resource, by accepting requests to production host at port 80 and forwarding them either to frontend **ui** or to backend **api** pods. The mapping is based on the HTTP request path, with those that contain "/api" being forwarded to the backend pod.  

Apply the ui-* YAML files by running:

```bash
kubectl apply -f ui-<name>.yaml
```

> **Note:** To enable SSL/TLS an existing certificate manager implementation that can be installed with `helm` was selected. You can install it by running:  
`helm3 install cert-manager jetstack/cert-manager --namespace cert-manager --create-namespace --version v1.8.0`
