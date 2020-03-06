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

4. Generate a Personal Access Token on GitHub with `repo`, `hooks`, and `user:email` accesses

5. Create a new GitHub server, from the Jenkins system settings, creating:
     * a "secretText" credential with the previously generated token
     * flagging "Manage hooks" (the Jenkins URL setting must be properly configured)

6. If your Jenkins certificate is self-signed, you need to go to the Webhooks configuration on your GitHub repo and make sure that the insecure delivery is flagged. The "Manage hooks" Jenkins settings (see point 2) should be then disabled in order to avoid the SSL verification setting reset

7. Create multi-branch declarative pipelines (preferably using the BlueOcean UI plugin), and enable branches, tags, and changes discovery on the pipeline configuration (using the Jenkins Classic UI). Moreover, after installing the [Basic Branch Build Strategies](https://plugins.jenkins.io/basic-branch-build-strategies) plugin, various build triggers can be configured for each discovered event
