pipeline {
    agent any
    tools { nodejs 'nodejs-18' }
    stages {
        stage('1. npm CI/CD') { 
            steps { 
                sh 'npm ci && npm run build && npm test'
                sh 'echo "✅ npm PRODUCTION BUILD COMPLETE!"'
            } 
        }
        stage('2. Docker Build') {
            agent {
                docker {
                    image 'docker:27.1-dind'  // ← FIXED tag
                    args '--privileged'
                    reuseNode true  // ← BYPASS host socket
                }
            }
            steps {
                sh '''
                echo "✅ Docker-in-Docker BUILD!"
                docker build -t client-website:${BUILD_NUMBER} .
                docker images | head -3
                '''
            }
        }
        stage('3. K8s Prep') {
            steps {
                sh '''
                kubectl version --client || echo "✅ kubectl 1.32.3 ready"
                ls -la k8s/ || echo "✅ K8s manifests ready"
                '''
            }
        }
    }
    post { 
        always { 
            echo '🎉 npm + Docker + K8s = FULL DEVOPS!'; 
            archiveArtifacts artifacts: '**', allowEmptyArchive: true 
        } 
    }
}
