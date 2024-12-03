pipeline {
    agent any
    tools {
        maven 'Maven'  // Configure Maven in Jenkins
    }
    environment {
        SONAR_TOKEN = credentials('sonar-token')  // Replace 'sonar-token' with the actual credentials ID in Jenkins
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Code Quality Check') {
            steps {
                withSonarQubeEnv('SonarQube') {  // 'SonarQube' is the name of your configured SonarQube server
                    sh 'mvn sonar:sonar'
                }
            }
        }
        stage('Quality Gate') {
            steps {
                waitForQualityGate abortPipeline: true
            }
        }
        stage('Code Coverage') {
            steps {
                jacoco execPattern: '**/target/*.exec', classPattern: '**/target/classes', sourcePattern: '**/src/main/java'
            }
        }
        stage('Security Scan') {
            steps {
                sh 'mvn org.owasp:dependency-check-maven:check'
            }
            post {
                always {
                    archiveArtifacts artifacts: '**/dependency-check-report.html', allowEmptyArchive: true
                }
            }
        }
    }
    post {
        success {
            mail to: 'nehadhama010@gmail.com',
                 subject: "Jenkins Build Success - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                 body: "The build ${env.JOB_NAME} #${env.BUILD_NUMBER} was successful."
        }
        failure {
            mail to: 'nehadhama010@gmail.com',
                 subject: "Jenkins Build Failure - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                 body: "The build ${env.JOB_NAME} #${env.BUILD_NUMBER} failed. Please check Jenkins for more details."
        }
    }
}
