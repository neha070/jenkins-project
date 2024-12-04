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
                        sh '''
                            mvn sonar:sonar \
                                -Dsonar.projectKey=jenkins-project \
                                -Dsonar.host.url=http://34.56.102.242:9000/ \
                                -Dsonar.login=sqp_e5c47caca6ccfd2c1840559e947dca707566f3fe
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

//         stage('Security Scan') {
//             agent {
//                 docker {
//                     image 'owasp/dependency-check:latest' // Use the Docker image for OWASP Dependency Check
//                     args '-v /var/run/docker.sock:/var/run/docker.sock -v $(pwd):/src -v $(pwd)/dependency-check:/report'
//                 }
//             }
//             steps {
//                 sh '''
//                     dependency-check.sh \
//                         --scan /src \
//                         --out /report \
//                         --format HTML
//                 '''
//             }
//             post {
//                 always {
//                     archiveArtifacts artifacts: 'dependency-check-report.html', allowEmptyArchive: true
//                 }
//             }
//         }
//     }
//     post {
//         success {
//             mail to: 'nehadhama010@gmail.com',
//                  subject: "Jenkins Build Success - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
//                  body: "The build ${env.JOB_NAME} #${env.BUILD_NUMBER} was successful."
//         }
//         failure {
//             mail to: 'nehadhama010@gmail.com',
//                  subject: "Jenkins Build Failure - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
//                  body: "The build ${env.JOB_NAME} #${env.BUILD_NUMBER} failed. Please check Jenkins for more details."
//         }
//     }
}
}
