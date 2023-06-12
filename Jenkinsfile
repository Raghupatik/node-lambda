pipeline {
    agent any

    environment {
        S3_BUCKET = 'raghu-jenkinsartifacts'
        LAMBDA_FUNCTION = 'test'
    }

    stages {
        stage('Build') {
            steps {
                script {
                    echo 'Build'
                    sh 'npm build'
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    echo 'Test'
                    sh 'npm test'
                }
            }
        }

        stage('Push to artifactory') {
            steps {
                script {
                    echo 'Push to artifactory'         

                    sh "aws s3 cp . s3://$S3_BUCKET"
                }
            }
        }

        stage('Deploy to Lambda - Test') {
            agent any
            steps {
                script {
                    echo 'Deploy to Test'

                    // sh "aws lambda update-function-code --function-name $LAMBDA_FUNCTION  --zip-file fileb://target/${JARNAME}"
                }          
            }
        }

        stage('Release to Prod') {
            steps {
                echo 'Release to Prod'
                script {
                    if (env.BRANCH_NAME == "main") {
                        timeout(time: 1, unit: 'HOURS') {
                            input('Proceed for Prod  ?')
                        }
                    }
                }

            }
        }

         stage('Deploy to Prod') {
            agent none
            steps {
                script {
                    if (env.BRANCH_NAME == "main") {
                        echo 'Deploy to Prod'
                        
                       // sh "aws lambda update-function-code --function-name $LAMBDA_FUNCTION --s3-bucket $S3_BUCKET --s3-key ${JARNAME}" 
                    }
                }
            }
        }

    }

    post {
      failure {
        echo 'failed'
        echo "${env.BUILD_NUMBER}"
          
         // can send notifications
      }
      success {
        echo 'Success'
      }
      aborted {
        echo 'aborted'
      }
    }
}
