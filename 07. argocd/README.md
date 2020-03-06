1. Create the Namespace:
    ```
    kubectl create namespace argocd
    ```

2. Install the chart:
    ```
    helm install --name cd argo/argo-cd --namespace argocd --values values.yaml
    ```

3. Install the CLI:
    ```
    brew tap argoproj/tap
    brew install argoproj/tap/argocd
    ```

5. Retrieve the current pwd (server Pod name):
    ```
    kubectl get pods -n argocd -l app.kubernetes.io/name=argocd-server -o name | cut -d'/' -f 2
    ```

4. Login with the CLI:
    ```
    argocd login $(kubectl describe ingress -n default | grep 'Address:' | awk '{print $2}') --grpc-web
    ```

5. Update the pwd:
    ```
    argocd account update-password
    ```
