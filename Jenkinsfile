pipeline {
    agent any

    environment {
        GITHUB_REPO = 'https://github.com/ansh0123456/TASK6.2C-jenkins.git'
        EMAIL_RECIPIENTS = 'taneja9688@gmail.com'
    }

    stages {
        stage('Build') {
            steps {
                echo 'Building application...'
                sh 'mvn clean package'  // Use Maven for Java projects
            }
        }

        stage('Unit and Integration Tests') {
            steps {
                echo 'Running unit and integration tests...'
                sh 'mvn test'  // Run tests
            }
        }

        stage('Code Analysis') {
            steps {
                echo 'Performing code analysis...'
                sh 'sonar-scanner'  // Use SonarQube for analysis
            }
        }

        stage('Security Scan') {
            steps {
                echo 'Running security scan...'
                sh 'trivy fs .'  // Use Trivy for security scanning
            }
            post {
                always {
                    mail to: "${EMAIL_RECIPIENTS}", 
                         subject: "Security Scan Status", 
                         body: "Check logs for security scan status.", 
                         attachLog: true
                }
            }
        }

        stage('Deploy to Staging') {
            steps {
                echo 'Deploying to Staging...'
                sh 'scp target/app.jar user@staging-server:/app/'
            }
        }

        stage('Integration Tests on Staging') {
            steps {
                echo 'Running tests on staging environment...'
                sh 'ssh user@staging-server "cd /app && ./run-tests.sh"'
            }
        }

        stage('Deploy to Production') {
            steps {
                echo 'Deploying to Production...'
                sh 'scp target/app.jar user@production-server:/app/'
            }
        }
    }

    post {
        failure {
            mail to: "${EMAIL_RECIPIENTS}", 
                 subject: "Pipeline Failed", 
                 body: "The pipeline has failed. Please check Jenkins logs.", 
                 attachLog: true
        }
    }
}
