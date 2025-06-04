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
        stage('OWASP Dependencies Check') {
            steps {
                dependencyCheck additionalArguments: '''
                    --scan \'./\'                                      #  Scan the current directory (root directory)
                    --out \'./\'                                       # Output to the current directory (root directory)
                    --format \'ALL\'                                   # Generate all formats
                    --pretty-print''', odcInstallation: 'OWASP-DepCheck-10'

            }
        }
    }
}