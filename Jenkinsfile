pipeline {
    agent none
    stages {
        stage('Build Docker Image') {
             agent {
    kubernetes {
      inheritFrom 'kaniko'
      yaml """
kind: Pod
metadata:
  name: kaniko
  namespace: devops-tools
spec:
  containers:
  - name: kaniko
    image: gcr.io/kaniko-project/executor:debug
    imagePullPolicy: Always
    command:
    - /busybox/cat
    tty: true
    volumeMounts:
      - name: kaniko-secret
        mountPath: /kaniko/.docker
  volumes:
  - name: kaniko-secret
    secret:
      secretName: regcred
      items:
        - key: .dockerconfigjson
          path: config.json
"""
    }
  }
            steps {
                container('kaniko') {
                    script {
                        sh """
                        /kaniko/executor \
                        --dockerfile ./Dockerfile \
                        --destination=core.harbor.domain/library/test_api:${BUILD_NUMBER} \
                        --skip-tls-verify \
                        --context `pwd`
                        """
                    }
                }
            }

        }
    }
}
