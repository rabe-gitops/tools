1. Install Helm (macOS):
    ```
    brew install helm@2
    ```

2. Create the ServiceAccount:
    ```
    kubectl create serviceaccount -n kube-system tiller
    ```

3. Create the ClusterRoleBinding:
    ```
    kubectl create clusterrolebinding tiller-cluster-admin --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
    ```

4. Init locally and remotely:
    ```
    helm init --history-max 10 --service-account tiller
    ```
