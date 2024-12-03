pipeline {
    agent any
    tools {
        maven 'Maven'
    }
    environment {
        SONAR_TOKEN = credentials('sonar-token') // Replace with your credential ID
    }
    stages {
        stage('Checkout') {
            steps {
                cleanWs()
                checkout scm
            }
        }
        stage('Build') {
            steps {
                dir('./') { // Root directory where pom.xml is located
                    sh 'mvn clean package'
                }
            }
        }
        stage('Cyclomatic Complexity') {
            steps {
                sh '/home/nehadhama010/lizard_env/bin/lizard src/main/java > complexity-report.txt'
            }
            post {
                always {
                   archiveArtifacts artifacts: 'complexity-report.txt', allowEmptyArchive: true
                }
            }
        }
        stage('Code Quality Check') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    dir('./') { // Root directory
                        sh 'mvn sonar:sonar'
                    }
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
                dir('./') { // Root directory
                    jacoco execPattern: '**/target/*.exec', classPattern: '**/target/classes', sourcePattern: '**/src/main/java'
                }
            }
        }
        stage('Security Scan') {
            steps {
                dir('./') { // Root directory
                    sh 'mvn org.owasp:dependency-check-maven:check'
                }
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
