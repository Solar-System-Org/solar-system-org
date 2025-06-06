pipeline {
    agent any

    tools {
        nodejs 'nodejs-22-6-0'
    }

    environment {
        MONGO_URI = "mongodb+srv://supercluster.d83jj.mongodb.net/superData"
    }

    stages {
        stage('Install Dependencies') {
            steps {
                sh 'npm install --no-audit'    // Add your test commands here
            }
        }
        
        // Run two stages in parallel
        // --scan \'./\'      #  Scan the current directory (root directory)
        // --out \'./\'       # Output to the current directory (root directory)
        // --format \'ALL\'   # Generate all formats     
        stage('Dependency Scanning') {
            parallel {
                stage('NPM Dependency Audit') {
                    steps {
                        sh '''
                            npm audit --audit-level=critical
                            echo $?
                        '''
                    }
                }
                // stage('OWASP Dependency Check') {
                //     steps {
                //         dependencyCheck additionalArguments: '''
                //             --scan \'./\' 
                //             --out \'./\'  
                //             --format \'ALL\' 
                //             --prettyPrint''', odcInstallation: 'OWASP-DepCheck-10'
                //         dependencyCheckPublisher failedTotalCritical: 1, pattern: 'dependency-check-report.xml', stopBuild: true
                        
                //         junit allowEmptyResults: true, stdioRetention: '', testResults: 'dependency-check-junit.xml'
                        
                //         publishHTML([
                //             allowMissing: true, 
                //             alwaysLinkToLastBuild: true, 
                //             keepAll: true, 
                //             reportDir: './', 
                //             reportFiles: 'dependency-check-jenkins.html', 
                //             reportName: 'Dependency Check HTML Report', 
                //             reportTitles: '', 
                //             useWrapperFileDirectly: true
                //         ])
                //     }
                // }
            }
        }
        stage('Unit Tests') {
            steps {
                echo "Performing unit tests"
                echo "junit will help archive the test results"
            }
            // steps {
            //     withCredentials([usernamePassword(credentialsId: 'mongo-db-creds', passwordVariable: 'MONGO_PASSWORD', usernameVariable: 'MONGO_USERNAME')]) {
            //         sh 'npm test'
            //     }
            //     junit allowEmptyResults: true, stdioRetention: '', testResults: 'test-results.xml'
            // }
        }
    }
}