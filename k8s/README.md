# Deploy with Kubernetes

The functional components to be deployed are the following:
- db (PostgreSQL)
- api (Java/Spring)
- ui (JS/Angular)

Many other components are required to orchestrate the deployment, like Secrets, Services, ConfigMaps, Ingress controllers etc.

In following paragraphs is described the complete deployment procedure.

## Database (db)

A **PostgreSQL** database is used to persist app data. We installed it to the k8s cluster using `helm`. 
