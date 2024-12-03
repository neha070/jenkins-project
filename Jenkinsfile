pipeline {
    agent any
    tools {
        maven 'Maven'
    }
    environment {
        SONAR_TOKEN = credentials('sonar-token') // Replace with your credential ID
    }
        stage('Checkout') {
            steps {
                cleanWs()
                checkout scm
            }
        }
        stage('Build') {
            steps {
                dir('src/test/java/com/example') { // Navigate to the directory containing pom.xml
                    sh 'mvn clean package'
                }
            }
        }
        stage('Cyclomatic Complexity') {
            steps {
                sh 'lizard src/main/java > complexity-report.txt'
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
                    dir('src/test/java/com/example') { // Navigate to the directory containing pom.xml
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
                dir('src/test/java/com/example') { // Navigate to the directory containing pom.xml
                    jacoco execPattern: '**/target/*.exec', classPattern: '**/target/classes', sourcePattern: '**/src/main/java'
                }
            }
        }
        stage('Security Scan') {
            steps {
                dir('src/test/java/com/example') { // Navigate to the directory containing pom.xml
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
