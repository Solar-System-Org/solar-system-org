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
                stage('OWASP Dependency Check') {
                    steps {
                        dependencyCheck additionalArguments: '''
                            --scan \'./\' 
                            --out \'./\'  
                            --format \'ALL\' 
                            --disableYarnAudit \
                            --prettyPrint --failOnCVSS 9''', nvdCredentialsId: 'owasp-dependency-check', odcInstallation: 'OWASP-DepCheck-10'
                        dependencyCheckPublisher failedTotalCritical: 1, pattern: 'dependency-check-report.xml', stopBuild: true
                        junit allowEmptyResults: true, stdioRetention: '', testResults: 'dependency-check-junit.xml'
                        publishHTML([allowMissing: true, alwaysLinkToLastBuild: true, keepAll: true, reportDir: './', reportFiles: 'dependency-check-jenkins.html', reportName: 'Dependency Check HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                    }
                }
            }
        }
        // Run two stages in parallel
        // --scan \'./\'      #  Scan the current directory (root directory)
        // --out \'./\'       # Output to the current directory (root directory)
        // --format \'ALL\'   # Generate all formats        
        // stage('Dependencies Scanning') {
        //     parallel {
        //         stage('OWASP Dependency Check') {
        //             steps {
        //                 dependencyCheck additionalArguments: '''
        //                     --scan \'./\'                                      
        //                     --out \'./\'                                       
        //                     --format \'ALL\'                                  
        //                     --pretty-print''', odcInstallation: 'OWASP-DepCheck-10'
                        
        //                 dependencyCheckPublisher failedTotalCritical: 2, pattern: 'dependency-check-report.xml', stopBuild: true 
                        
        //                 junit allowEmptyResults: true, stdioRetention: '', testResults: 'dependency-check-junit.xml'

        //                 publishHTML([
        //                     allowMissing: true,
        //                     alwaysLinkToLastBuild: true,
        //                     keepAll: true,
        //                     reportDir: './',
        //                     reportFiles: 'dependency-check-jenkins.html',
        //                     reportName: 'Dependency Check HTML Report',
        //                     reportTitles: '',
        //                     userWrapperFileDirctly: true
        //                 ])
        //             }
        //         }
        //         stage('NPM Audit') {
        //             steps {
        //                 sh '''
        //                     npm audit --audit-level=critical
        //                 '''
        //             }
        //         }
        //     }

        // }
    }
}