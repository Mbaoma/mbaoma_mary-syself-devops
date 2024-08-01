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