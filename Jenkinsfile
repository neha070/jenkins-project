pipeline {
    agent any
    tools {
        maven 'Maven'
    }
    environment {
        SONAR_TOKEN = credentials('sonar-token') // Replace with your credential ID
        SONAR_PROJECT_KEY = credentials('github-sonar-project-key') // Replace with Jenkins credential ID
        SONAR_HOST_URL = credentials('github-sonar-host-url')      // Replace with Jenkins credential ID
        SONAR_LOGIN = credentials('github-sonar-login')
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
                        sh '''
                            mvn sonar:sonar \
                                -Dsonar.projectKey=$SONAR_PROJECT_KEY \
                                -Dsonar.host.url=$SONAR_HOST_URL \
                                -Dsonar.login=$SONAR_LOGIN
                        '''
                    }
                }
            }
        }
        stage('Quality Gate') {
            steps {
                script {
                    timeout(time: 30, unit: 'MINUTES') { // Set timeout for Quality Gate stage
                        waitForQualityGate abortPipeline: true
                    }
                }
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
                script {
                    // Run Trivy scan and generate JSON and HTML reports
                    sh 'trivy fs --exit-code 1 --severity HIGH --format json --output trivy-report.json ./src'
                    sh 'trivy fs --exit-code 1 --severity HIGH --format template --template /home/nehadhama010/templates/html.tpl --output trivy-report.html ./src'
                }
            }
            post {
                always {
                    // Archive Reports
                    archiveArtifacts artifacts: 'trivy-report.json, trivy-report.html', allowEmptyArchive: true
        
                    // Publish HTML Report
                    publishHTML(target: [
                        allowMissing: true,
                        alwaysLinkToLastBuild: true,
                        keepAll: true,
                        reportDir: '.',
                        reportFiles: 'trivy-report.html',
                        reportName: 'Trivy Vulnerability Report'
                    ])
                }
            }
        }
}
}
