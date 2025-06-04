pipeline {
    agent any

    tools {
        nodejs 'nodejs-22-6-0'
    }

    stages {
        stage('Install Dependencies') {
            steps {
                sh 'npm install --no-audit'    // Add your test commands here
            }
        }
        stage('NPM Dependencies Audit') {
            steps {
                sh '''
                    npm audit --audit-level=critical
                '''
            }
        }
    }
}