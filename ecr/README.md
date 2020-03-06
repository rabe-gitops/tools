1. Create the ConfigMap (do not change config.json file name):
```
kubectl create configmap docker-config -n jenkinsci --from-file=config.json
```
