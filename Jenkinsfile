pipeline {
    agent any
    options {
        buildDiscarder(logRotator(numToKeepStr: '10'))
        timeout(time: 30, unit: 'MINUTES')
        timestamps()
    }
    tools { maven 'maven-3.9' }
    parameters {
        choice(name: 'ENV', choices: ['dev', 'staging', 'prod'], description: 'Target env')
    }
    environment {
        IMAGE_TAG = "v${BUILD_NUMBER}"
    }
    stages {
        stage('Checkout') { steps { checkout scm } }
        stage('Build')    { steps { sh 'mvn clean package' } }
        stage('Test')     { steps { sh 'mvn test' } }
        stage('Deploy to Prod') {
            when { expression { params.ENV == 'prod' } }
            steps { sh 'kubectl apply -f k8s/prod/' }
        }
    }
    post {
        always  { cleanWs() }
        failure { echo "Build failed — see ${BUILD_URL}" }
    }
}
