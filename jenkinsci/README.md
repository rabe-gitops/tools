1. Create the Namespace:
```
kubectl create namespace jenkinsci
```

2. Install the chart:
```
helm install -n ci stable/jenkins --namespace jenkinsci --values values.yaml
```

3. Retrieve the pwd:
```
kubectl get secret --namespace jenkinsci ci-jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode
```
