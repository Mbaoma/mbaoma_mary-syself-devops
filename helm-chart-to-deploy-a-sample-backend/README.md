# helm-chart
This is a helm chart that deploys applications to a Kubernetes cluster. The Chart is hosted on an S3 bucket.


## Creating a remote Helm Chart
- Install [Helm](https://helm.sh).

- Package the Helm Chart
```
helm package syself
```
This command will create a .tgz file (e.g., syself-app-0.1.0.tgz) in your directory.

- Upload the Chart to S3 (or any cloud storage)

```
aws s3 cp syself-0.1.0.tgz s3://syself-helm-chart-01/
```

- Generate an Index File
```
helm repo index . --url s3://syself-helm-chart-01/
```

- Upload the generated index.yaml to your S3 bucket
```
aws s3 cp index.yaml s3://syself-helm-chart-01/
```

- Configure Helm to use the S3 Bucket as a Repository
```
helm plugin install https://github.com/hypnoglow/helm-s3.git
helm s3 init s3://syself-helm-chart-01/
helm repo add syself s3://syself-helm-chart-01/
helm repo update
```
Checkout more options on how to [host](https://helm.sh/docs/topics/chart_repository/) a helm Chart

## Installation Guide
- [Add](https://helm.sh/docs/topics/chart_repository/) your repository:
```
helm repo add syself s3://syself-helm-chart-01/
```

### To install the chart:
```
helm install chart-name syself/syself
```

### To uninstall the chart:
```
helm uninstall syself
```

[Helm Documentation](https://helm.sh/docs)

## Helm Chart Components Explanation
- **namespace**: defines where the application is deployed.
- **replicaCount**: should be set to a default value of 3 for high availability.
- **image**: I used nginx but users can use any custom image name or define a template such as ```image-repo/image-name```.
- **tag**: define your image's tag here, or use a place holder which you can change using a bash script in your CI/CD pipeline.
- **service.yaml**: by default all nodes will be deployed as serviceType of ```ClusterIP```, but users can change this.
- **ingress.yaml**: users can configure this to expose apps externally.
- **Resource Limits**: users can configure these to efficiently use resources.
- **Node Tolerations**: users should set this up, so pods are provisioned according to business needs.

## Big Picture
For a full implementation, I will setup a CI/CD pipeline which will have two stages - build stage and deploy stage. ```build``` stage will do the following:
- run tests
- build a container image of my app
- push the container image to an image repository such as Elastic Container Registry (ECR)

While the ```deploy``` stage will:
- authenticate to my Kubernetes cluster
- install helm
- deploy my newly built image (from build stage) to my cluster according to my customized ```value.yaml``` chart in my app's repository.

I will conclude my cluster's configuration by setting up:
- SSL (for secure connections)
- Ingress
- auto scaling (to automatically scale up to create more pods when certain CPU or memory usage conditions are met)
- a monitoring stack (install helm Charts of Grafana, Prometheus, or any monitoring tool of choice)
- Hashicorp Vault for more secure app secrets (environment variables) management and storage

## Deploying a Database
I will use a managed database service like Amazon RDS (AWS RDS) or Google Cloud SQL, as they offer the following:
- **High Availability**: they provide built-in High Availability (HA) configurations, like multiple availability zones (multi-AZ) deployments.
- **Automatic Backups**: providers like Amazon RDS have built-in automated backups.
- **Scalability**: they easily scale up or down based on demand.
- **Security**: they offer enhanced security features like managed access (using IAM), encryption at rest and in transit, automated patching.

## Tools
I will use:
- Terraform to define IT assets (Kubernetes Cluster, database, storage buckets etc) and apply those assets in the cloud ensuring consistent, version-controlled, and easy to manage deployments
- Helm to manage Kubernetes resources and ensure applications are deployed with the correct configuration.

## Summary
I took this deployment approach for the following reasons:
- **Resilience**: cloud managed services offer better resilience by default.
- It reduces operational overhead as the cloud provider handles many routine tasks like patching software giving my team more time to focus on improving and developing core business features.

The Helm Chart and database deployment approach provide a production-ready application deployment in Kubernetes - enabling high availability and scalability.