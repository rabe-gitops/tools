1. Create the Namespace:
    ```
    kubectl create namespace jenkinsci
    ```

2. Install the chart:
    ```
    helm install -n ci stable/jenkins --namespace jenkinsci --values values.yaml
    ```

Note: you can skip steps from 3 to 9 if you directly attach the needed policies on the worker nodes IAM Role (not recommended).

3. Create the slaves ServiceAccount:
    ```
    kubectl create serviceaccount jenkinsci-slave-serviceaccount -n jenkinsci
    ```

4. Create the slaves ClusterRole (read-only on every resource, needed for the manifests validation):
    ```
    kubectl create clusterrole jenkinsci-slave-clusterrole --verb=get,list,watch --resource='*.*'
    ```

5. Create the slaves ClusterRoleBinding:
    ```
    kubectl create clusterrolebinding jenkinsci-slave-clusterrolebinding --clusterrole=jenkinsci-slave-clusterrole --serviceaccount=jenkinsci:jenkinsci-slave-serviceaccount
    ```

6. Retrieve the OpenID Identity Provider URL:
    ```
    aws eks describe-cluster --name <CLUSTER_NAME> --query "cluster.identity.oidc.issuer" --output text --profile <PROFILE_NAME>
    ```

7. Create an IAM Identity Provider, specifying:
    * Provider Type: `OpenID Connect`
    * Provider URL: _PREVIOUSLY-OBTAINED-URL_
    * Audience: `sts.amazonaws.com`

8. Create an IAM Role, with:
    * Trusted Entity: _PREVIOUSLY-DEFINED-IDENTITY-PROVIDER_
    * Policy: _the needed managed policies (e.g. AmazonEC2ContainerRegistryPowerUser)_

9. Annotate the Jenkins ServiceAccount with the Role ARN:
    ```
    kubectl annotate serviceaccount -n jenkinsci jenkinsci-slave-serviceaccount eks.amazonaws.com/role-arn=<ROLE_ARN>
    ```

10. Retrieve the pwd:
    ```
    kubectl get secret --namespace jenkinsci ci-jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode
    ```

11. Generate a Personal Access Token on GitHub with `repo`, `hooks`, and `user:email` accesses

12. Create a new GitHub server, from the Jenkins system settings, creating:
     * a "secretText" credential with the previously generated token
     * flagging "Manage hooks" (the Jenkins URL setting must be properly configured)

13. If your Jenkins certificate is self-signed, you need to go to the Webhooks configuration on your GitHub repo and make sure that the insecure delivery is flagged. The "Manage hooks" Jenkins settings (see point 2) should be then disabled in order to avoid the SSL verification setting reset

14. Create multi-branch declarative pipelines (preferably using the BlueOcean UI plugin), and enable branches, tags, and changes discovery on the pipeline configuration (using the Jenkins Classic UI). Moreover, after installing the [Basic Branch Build Strategies](https://plugins.jenkins.io/basic-branch-build-strategies) plugin, various build triggers can be configured for each discovered event

15. Configure the various slaves (Pod Templates) to use:
    * the `jenkinsci` namespace
    * the `jenkinsci-slave-serviceaccount` service account
    * `/bin/sh -c` as entrypoint and `cat` as command (or `busybox/cat` as entrypoint for some images)
    * flag the `allocate pseudo-TTY` option
