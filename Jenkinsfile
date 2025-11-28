pipeline {
    agent any
    tools {
        nodejs 'nodejs-18'
    }
    stages {
        stage('Checkout') {
            steps {
                echo '✅ Checkout code'
            }
        }
        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }
        stage('Build') {
            steps {
                sh 'npm run build'
            }
        }
    }
    post {
        always {
            echo '🎉 Pipeline COMPLETE!'
            archiveArtifacts artifacts: 'dist/**, build/**', allowEmptyArchive: true
        }
    }
}
