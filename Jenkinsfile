pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Repository Info') {
            steps {
                sh '''
                    echo "=== Git information ==="
                    git log -1 --oneline

                    echo
                    echo "=== Repository files ==="
                    find apps -type f
                '''
            }
        }

        stage('Validate Manifests') {
            steps {
                sh '''
                    echo "=== Deployment ==="
                    grep "replicas:" apps/nginx/deployment.yaml

                    echo
                    echo "=== Service ==="
                    grep -E "type:|nodePort:" apps/nginx/service.yaml
                '''
            }
        }

        stage('GitOps Handoff') {
            steps {
                echo 'CI validation completed.'
                echo 'Argo CD remains responsible for Kubernetes deployment.'
            }
        }
    }

    post {
        success {
            echo 'Jenkins CI pipeline completed successfully!'
        }

        failure {
            echo 'Jenkins CI pipeline failed!'
        }
    }
}
