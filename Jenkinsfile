pipeline {
    agent any

    environment {
        EMAIL_RECIPIENTS = 'taneja9688@gmail.com'
    }

    stages {
        stage('Build') {
            steps {
                echo 'Building the project...'
                sh 'mvn clean package'  // Adjust if using a different build tool
            }
        }

        stage('Unit and Integration Tests') {
            steps {
                echo 'Running Unit and Integration Tests...'
                sh 'mvn test'  // Adjust if using another test framework
            }
            post {
                failure {
                    script {
                        def log = currentBuild.rawBuild.getLog(50).join("\n")
                        mail to: "${EMAIL_RECIPIENTS}",
                             subject: "Unit/Integration Tests Failed",
                             body: "Tests failed. Check logs:\n\n${log}"
                    }
                }
            }
        }

        stage('Code Analysis') {
            steps {
                echo 'Running Static Code Analysis...'
                sh 'sonar-scanner'  // Assuming SonarQube is set up
            }
        }

        stage('Security Scan') {
            steps {
                echo 'Performing Security Scan...'
                sh 'trivy filesystem .'  // Example using Trivy for security scanning
            }
            post {
                failure {
                    script {
                        def log = currentBuild.rawBuild.getLog(50).join("\n")
                        mail to: "${EMAIL_RECIPIENTS}",
                             subject: "Security Scan Failed",
                             body: "Security vulnerabilities found. Check logs:\n\n${log}"
                    }
                }
            }
        }

        stage('Deploy to Staging') {
            steps {
                echo 'Deploying to Staging...'
                sh 'scp target/*.jar user@staging-server:/app/'  // Adjust for your deployment
            }
        }

        stage('Integration Tests on Staging') {
            steps {
                echo 'Running Integration Tests on Staging...'
                sh 'curl -X GET http://staging-server/healthcheck'
            }
        }

        stage('Deploy to Production') {
            steps {
                echo 'Deploying to Production...'
                sh 'scp target/*.jar user@production-server:/app/'  // Adjust for your production setup
            }
        }
    }

    post {
        success {
            mail to: "${EMAIL_RECIPIENTS}",
                 subject: "Jenkins Pipeline Success",
                 body: "The pipeline ran successfully!"
        }

        failure {
            script {
                def log = currentBuild.rawBuild.getLog(50).join("\n")
                mail to: "${EMAIL_RECIPIENTS}",
                     subject: "Jenkins Pipeline Failed",
                     body: "Pipeline failed. Check logs:\n\n${log}"
            }
        }
    }
}
