1. Add the repo:
    ```
    helm repo add incubator http://storage.googleapis.com/kubernetes-charts-incubator
    ```

2. Tag the private and public subnets as described [here](https://docs.aws.amazon.com/eks/latest/userguide/alb-ingress.html)

3. Install the controller:
    ```
    helm install incubator/aws-alb-ingress-controller -n alb --set clusterName=rabe-gitops-cluster --set autoDiscoverAwsRegion=true --set autoDiscoverAwsVpcID=true --set image.repository=docker.io/amazon/aws-alb-ingress-controller --set image.tag=v1.2.0-alpha.1 --namespace kube-system
    ```

4. Add [this](https://github.com/kubernetes-sigs/aws-alb-ingress-controller/blob/master/docs/examples/iam-policy.json) policy to the EC2 worker nodes role

5. Deploy a common ALB with default redirect from 80 to 443:
    ```
    kubectl apply -n default -f common-alb.yaml
    ```
