pipeline {
    agent none

    environment {
        IMAGE_NAME = "django-ecommerce"
        IMAGE_TAR  = "django-ecommerce.tar"
    }

    stages {
        stage('Build Docker Image with Kaniko (TAR only)') {
            agent {
                kubernetes {
                    yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: kaniko
    image: gcr.io/kaniko-project/executor:v1.23.2
    command:
    - sleep
    args:
    - infinity
    volumeMounts:
    - name: workspace-volume
      mountPath: /workspace
  volumes:
  - name: workspace-volume
    emptyDir: {}
"""
                }
            }

            steps {
                container('kaniko') {
                    sh '''
                    echo "Starting Kaniko build for Django application"

                    /kaniko/executor \
                      --context `pwd` \
                      --dockerfile Dockerfile \
                      --tar-path=/workspace/${IMAGE_TAR} \
                      --no-push \
                      --verbosity=info
                    '''
                }

                archiveArtifacts artifacts: '*.tar', fingerprint: true
            }
        }
    }

    post {
        success {
            echo "Docker image successfully built and stored as TAR artifact"
        }
        failure {
            echo "Kaniko build failed"
        }
    }
}
