1. Create the ConfigMap for ECR (do not change the `config.json` file name):
    ```
    kubectl create configmap docker-config -n jenkinsci --from-file=config.json
    ```

2. Configure a Jenkins Kubernetes Pod Template and Container template, using the following values, in the Jenkins system settings:

    The `debug-539ddefcae3fd6b411a95982a830d987f4214251` tag on the image is currently fundamental to make it work with the Jenkins Kubernetes plugin! Newer versions seem to cause Pods to be stuck on the terminating state.

    * Pod Template:
      * name: `ci-kaniko`
      * namespace: `jenkinsci`
      * label: `kaniko-slave`

    * container template:
      * name: `kaniko`
      * image: `gcr.io/kaniko-project/executor:debug-539ddefcae3fd6b411a95982a830d987f4214251`
      * imagePullPolicy: `always`
      * command: `/busybox/cat`
      * tty: `true`
      * volumes (of type ConfigMap):
        * name: `docker-config`
        * mountPath: `/kaniko/.docker`

3. Use it as follows, in the Jenkinsfile:
    ```
    agent {
      kubernetes {
        label 'kaniko-slave'
      }
    }
    steps {
      // Select container inside Pod
      container('kaniko') {
        sh """
          /kaniko/executor \
          --dockerfile \$(pwd)/Dockerfile \
          --context \$(pwd) \
          --destination=${AWS_ACCOUNT}.dkr.ecr.${AWS_REGION}.amazonaws.com/${REPO_NAME}:latest
      """
      }
    }
    ```
