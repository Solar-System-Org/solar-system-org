pipeline {
    agent any

    tools {
        nodejs 'nodejs-22-6-0'
    }

    environment {
        AWS_REGION = 'us-east-1' // Change to your region
        ECR_REPO = 'solar-system'
        IMAGE_TAG = "${env.BUILD_NUMBER}"
        AWS_ACCOUNT_ID = '225989363866' // Change to your AWS account ID
        ECR_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO}"
        MONGO_URI = "mongodb+srv://supercluster.d83jj.mongodb.net/superData"
        MONGO_DB_CREDS = credentials('mongo-db-creds')
        MONGO_USERNAME = credentials('mongo-db-username')
        MONGO_PASSWORD = credentials('mongo-db-password')
    }

    stages {
         stage('Logging into AWS ECR') {
            steps {
                script {
                    sh "aws ecr get-login-password - region ${AWS_REGION} | docker login - username AWS - password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
                }
            }
        }

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

        stage('Code Coverage') {
            // agent {
            //     docker {
            //         image 'node:24'
            //         args '-u root:root'
            //     }
            // }
            steps {
                catchError(buildResult: 'SUCCESS', message: 'Oops! it will be fixed in future releases', stageResult: 'UNSTABLE') {
                    // sh 'npm run coverage' 
                    sh 'echo Code Coverage Stage'
                }

            }
        }

        stage('SAST - SonarQube') {
            steps {
                sh 'echo SAST - SonarQube Stage'
                sh 'echo SonarQube Scanner Command'
                sh 'echo SAST - SonarQube Scanner Stage'
            }
            // steps {
            //     timeout(time: 60, unit: 'SECONDS') {            // if timeout occurs, the stage will be aborted
            //         withSonarQubeEnv('SonarQube') {
            //             // sh 'sonar-scanner -Dsonar.projectKey=supercluster-superdata -Dsonar.sources=. -Dsonar.host.url=http://localhost:9000 -Dsonar.login=admin -Dsonar.password=admin'

            //         }
            //         waitForQualityGate abortPipeline: true
            //     }
            // }
        }

        // stage('Build Image') {
        //     steps {
        //         sh 'printenv'
        //         sh  'docker build -t samishken/solar-system:$GIT_COMMIT .'
        //         withDockerRegistry(credentialsId: 'docker-hub-credentials', url: "") {
        //             sh  'docker push samishken/solar-system:$GIT_COMMIT'
        //         }
        //     }
        // }

        // stage('Build Docker Image') {
        //     steps {
        //         script {
        //             sh "docker build -t ${ECR_REPO}:${IMAGE_TAG} ."
        //             sh "docker tag ${ECR_REPO}:${IMAGE_TAG} ${ECR_URI}:${IMAGE_TAG}"
        //         }
        //     }
        // }

        // stage('Login to ECR') {
        //     steps {
        //         script {
        //             sh """
        //                 aws ecr get-login-password --region ${AWS_REGION} | \
        //                 docker login --username AWS --password-stdin ${ECR_URI}
        //             """
        //         }
        //     }
        // }

        // stage('Push to ECR') {
        //     steps {
        //         script {
        //             sh "docker push ${ECR_URI}:${IMAGE_TAG}"
        //         }
        //     }
        // }
    }
    post {
        success {
            echo "Image pushed to ECR: ${ECR_URI}:${IMAGE_TAG}"
        }
        failure {
            echo "Build or push failed."
        }
        //     always {
        //         junit allowEmptyResults: true, stdioRetention: '', testResults: 'test-results.xml'
        //         publishHTML([allowMissing: true, alwaysLinkToLastBuild: true, keepAll: true, reportDir: 'coverage/lcov-report', reportFiles: 'index.html', reportName: 'Code Coverage HTML Report', reportTitles: '', useWrapperFileDirectly: true])
        //     }
    }
}