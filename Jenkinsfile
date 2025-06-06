pipeline {
    agent any

    tools {
        nodejs 'nodejs-22-6-0'
    }

    environment {
        MONGO_URI = "mongodb+srv://supercluster.d83jj.mongodb.net/superData"
        MONGO_DB_CREDS = credentials('mongo-db-creds')
        MONGO_USERNAME = credentials('mongo-db-username')
        MONGO_PASSWORD = credentials('mongo-db-password')
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
            options {
                retry(2)
            }
            steps {
                sh 'echo Colon-Separated - $MONGO_DB_CREDS'
                sh 'echo Username - $MONGO_DB_CREDS_USR'
                sh 'echo Password - $MONGO_DB_CREDS_PSW'
                // sh 'npm test'
                
            }
        }

        // steps {
        //     withCredentials([usernamePassword(credentialsId: 'mongo-db-creds', passwordVariable: 'MONGO_PASSWORD', usernameVariable: 'MONGO_USERNAME')]) {
        //         sh 'npm test'
        //     }
        //     junit allowEmptyResults: true, stdioRetention: '', testResults: 'test-results.xml'
        // }

        // stage('Code Coverage') {
        //     // agent {
        //     //     docker {
        //     //         image 'node:24'
        //     //         args '-u root:root'
        //     //     }
        //     // }
        //     steps {
        //         catchError(buildResult: 'SUCCESS', message: 'Oops! it will be fixed in future releases', stageResult: 'UNSTABLE') {
        //             sh 'npm run coverage'
        //         }

        //     }
        // }

        // post {
        //     always {
        //         junit allowEmptyResults: true, stdioRetention: '', testResults: 'test-results.xml'
        //         publishHTML([allowMissing: true, alwaysLinkToLastBuild: true, keepAll: true, reportDir: 'coverage/lcov-report', reportFiles: 'index.html', reportName: 'Code Coverage HTML Report', reportTitles: '', useWrapperFileDirectly: true])
        //     }
        // }
    }
}