1. Add the following policy to the worker nodes IAM Role (not recommended) or to a role for the External DNS Service Account (following [this](https://docs.aws.amazon.com/eks/latest/userguide/create-service-account-iam-policy-and-role.html) guide):
    ```
    {
      "Version": "2012-10-17",
      "Statement": [
        {
          "Effect": "Allow",
          "Action": [
            "route53:ChangeResourceRecordSets"
          ],
          "Resource": [
            "arn:aws:route53:::hostedzone/*"
          ]
        },
        {
          "Effect": "Allow",
          "Action": [
            "route53:ListHostedZones",
            "route53:ListResourceRecordSets"
          ],
          "Resource": [
            "*"
          ]
        }
      ]
    }
  ```

2. Add the Bitnami Helm repository:
    ```
    helm repo add bitnami https://charts.bitnami.com/bitnami
    ```

3. Run the following command:
    ```
    helm install -n r53 bitnami/external-dns --namespace kube-system --values values.yaml
    ```
