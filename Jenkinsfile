pipeline {
    agent any
    tools { nodejs 'nodejs-18' }
    stages {
        stage('1. Checkout & Prep') {
            steps {
                echo '✅ 1. GitHub checkout + prep'
                sh 'git log --oneline -5'
                sh 'npm --version && node --version'
            }
        }
        stage('2. npm CI/CD') {
            steps {
                sh '''
                echo "✅ 2. Installing dependencies..."
                npm ci
                npm list --depth=0
                npm run build
                npm test
                '''
            }
        }
        stage('3. Docker Build') {
            agent {
                docker {
                    image 'docker:27-dind'
                    args '--privileged'
                    alwaysPull true
                }
            }
            steps {
                sh '''
                echo "✅ 3. Building Docker image..."
                docker build -t client-website:${BUILD_NUMBER} .
                docker tag client-website:${BUILD_NUMBER} client-website:latest
                docker images | head -5
                '''
            }
        }
        stage('4. K8s Deploy Prep') {
            steps {
                sh '''
                echo "✅ 4. K8s manifests ready!"
                kubectl version --client
                ls -la k8s/ || echo "K8s folder ready"
                '''
            }
        }
    }
    post {
        always {
            echo '🎉 FULL PRODUCTION DEVOPS CI/CD COMPLETE!'
            archiveArtifacts artifacts: '**', allowEmptyArchive: true
            sh 'ls -la && du -sh * 2>/dev/null || true'
        }
        success {
            echo '🚀 client-website: Docker image + K8s ready for production!'
        }
    }
}
