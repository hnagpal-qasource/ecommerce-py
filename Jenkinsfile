pipeline {
    agent { label 'linux-deploy' }  

    environment {
        IMAGE_NAME = "django-ecommerce"
        IMAGE_TAR  = "django-ecommerce.tar"
    }

    stages {
        stage('Checkout Source') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image with Kaniko (TAR only)') {
            steps {
                sh '''
                echo "Starting Kaniko build on VM agent"

                /kaniko/executor \
                  --context `pwd` \
                  --dockerfile Dockerfile \
                  --tar-path=${IMAGE_TAR} \
                  --no-push \
                  --verbosity=info
                '''
            }
        }

        stage('Archive Image') {
            steps {
                archiveArtifacts artifacts: '*.tar', fingerprint: true
            }
        }
    }

    post {
        success {
            echo "Docker image built and archived successfully"
        }
        failure {
            echo "Kaniko build failed"
        }
    }
}
