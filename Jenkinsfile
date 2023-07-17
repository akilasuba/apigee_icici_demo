#!groovy

pipeline {
    agent any
    tools {
        maven 'Maven'
        nodejs 'Nodejs'
    }

    stages {
        stage('Initial-Checks') {
            steps {
                sh "npm -v"
                sh "mvn -v"
        }}  
        stage('Policy-Code Analysis') {
            steps {
                sh "npm install -g apigeelint"
                sh "apigeelint -s iciciproxy/apiproxy/ -f codeframe.js"
                sh "apigeelint -s security/sharedflowbundle/ -f codeframe.js"

            }
        }

        stage('Approval for UAT') {
            steps {
                script {
                    def approval = input message: 'Approve to deploy in production', ok: 'Approve'
                    echo "Approval: ${approval}"
                }
            }
        }
        stage('SharedFlow deployment to UAT') {
            steps {
                 // service account key from  credentials 
                withCredentials([file(credentialsId: 'sailorgcp', variable: 'MY_FILE')]) {
                    echo 'My file path: $MY_FILE'
                    //deploy using maven plugin and replace email and orgs value 
                    sh "mvn -f ./security/pom.xml install -Puat -Dorg=sailor-321711 -Dsafile=$MY_FILE -Demail=apigeetest@sailor-321711.iam.gserviceaccount.com"
                }
            }
        }
        stage('Deploy to UAT') {
            steps {
                 // service account key from  credentials 
                withCredentials([file(credentialsId: 'sailorgcp', variable: 'MY_FILE')]) {
                    echo 'My file path: $MY_FILE'
                    //deploy using maven plugin and replace email and orgs value 
                    sh "mvn -f ./iciciproxy/pom.xml install -Puat -Dorg=sailor-321711 -Dsafile=$MY_FILE -Demail=apigeetest@sailor-321711.iam.gserviceaccount.com"
                }
            }
        }
        stage('Approval for production') {
            steps {
                script {
                    def approval = input message: 'Approve to deploy in production', ok: 'Approve'
                    echo "Approval: ${approval}"
                }
            }
        }
        stage('Unit-Test-With-Coverage') {
            steps {
                  sh "npm install request-promise"
                  sh "npm install"
                  sh "npm run coverage"
            }
        }
        stage('Deploy to UAT') {
            steps {
                 // service account key from  credentials 
                withCredentials([file(credentialsId: 'sailorgcp', variable: 'MY_FILE')]) {
                    echo 'My file path: $MY_FILE'
                    //deploy using maven plugin and replace email and orgs value 
                    sh "mvn -f ./iciciproxy/pom.xml install -Puat -Dorg=sailor-321711 -Dsafile=$MY_FILE -Demail=apigeetest@sailor-321711.iam.gserviceaccount.com"
                }
            }
        }
        
    }
}
