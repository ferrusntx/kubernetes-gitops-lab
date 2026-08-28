pipeline {
    agent any

    stages {

        stage('Repository Info') {
            steps {
                sh '''
                    echo "=== Git information ==="
                    git log -1 --oneline
                '''
            }
        }

        stage('Validate Manifests') {
            steps {
                sh '''
                    echo "=== Current application version ==="
                    grep "Version" apps/nginx/configmap.yaml
                '''
            }
        }

        stage('Update GitOps Manifest') {
            steps {
                sh '''
                    sed -i 's/Version 1.0/Version 2.0 - Updated by Jenkins/' \
                    apps/nginx/configmap.yaml

                    echo "=== New version ==="
                    grep "Version" apps/nginx/configmap.yaml
                '''
            }
        }

        stage('Push GitOps Change') {
            steps {
                sshagent(credentials: ['github-jenkins-ssh']) {
                    sh '''
                        git config user.name "Jenkins CI"
                        git config user.email "jenkins@gitops-lab.local"

                        git remote set-url origin \
                        git@github.com:ferrusntx/kubernetes-gitops-lab.git

                        git add apps/nginx/configmap.yaml

                        git commit \
                        -m "Update application to version 2.0 from Jenkins"

                        git push origin HEAD:main
                    '''
                }
            }
        }

        stage('GitOps Handoff') {
            steps {
                echo 'GitOps manifest updated successfully.'
                echo 'Argo CD is responsible for deployment.'
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
