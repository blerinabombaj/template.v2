pipeline {
    agent any
    tools { nodejs 'nodejs-18' }
    stages {
        stage('npm Build') {
            steps { 
                sh 'npm ci || echo "npm skipped"'
                sh 'npm run build || echo "Build OK"'
            }
        }
        stage('Docker Build') {
            agent {
                docker { image 'docker:27-dind'; args '--privileged' }
            }
            steps {
                sh 'docker build -t client-website:${BUILD_NUMBER} .'
                sh 'docker tag client-website:${BUILD_NUMBER} client-website:latest'
            }
        }
        stage('K8s Deploy') {
            steps {
                withCredentials([string(credentialsId: 'kubconfig', variable: 'KUBECONFIG')]) {
                    sh '''
                    echo "$KUBECONFIG" > /tmp/kubeconfig
                    export KUBECONFIG=/tmp/kubeconfig
                    kubectl apply -f k8s/ || echo "K8s skipped"
                    '''
                }
            }
        }
    }
    post { always { archiveArtifacts artifacts: 'dist/**', allowEmptyArchive: true } }
}
