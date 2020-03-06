1. Configure a Jenkins Kubernetes Pod Template and Container template, using the following values, in the Jenkins system settings:

The `debug-539ddefcae3fd6b411a95982a830d987f4214251` tag on the image is currently fundamental to make it work with the Jenkins Kubernetes plugin! Any newer stable version will cause pods to be stuck on the terminating state.

* Pod Template:
  * name: ci-kaniko
  * namespace: jenkinsci
  * label: kaniko-slave

* container template:
  * name: kaniko
  * image: gcr.io/kaniko-project/executor:debug-539ddefcae3fd6b411a95982a830d987f4214251
  * imagePullPolicy: Always
  * command: /busybox/cat
  * tty: true
  * volumes (of type ConfigMap):
    * name: docker-config
    * mountPath: /kaniko/.docker

2. Use it as follows, in the Jenkinsfile:
```
agent {
  kubernetes {
    label 'kaniko-slave'
  }
}
steps {
  // Select container inside pod
  container('kaniko') {
    sh '''
    /kaniko/executor \
    --dockerfile `pwd`/Dockerfile \
    --context `pwd` \
    --destination=904573531492.dkr.ecr.eu-west-1.amazonaws.com/app:latest
  '''
  }
}
```
