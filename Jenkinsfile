pipeline {
    agent any

    environment {
        // Define any environment variables here
         PROJECT_NAME = 'my-project'
    }

    options {
        // Options to control the pipeline execution
        timeout(time: 1, unit: 'HOURS')  // Timeout after 1 hour
        timestamps()  // Add timestamps to the console output
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                script {
                    if (fileExists('build.gradle')) {
                        sh './gradlew build'
                    } else if (fileExists('pom.xml')) {
                        sh 'mvn clean install'
                    } else {
                        error 'No build file found!'
                    }
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    if (fileExists('build.gradle')) {
                        sh './gradlew test'
                    } else if (fileExists('pom.xml')) {
                        sh 'mvn test'
                    } else {
                        error 'No build file found!'
                    }
                }
            }
        }

        stage('Quality Checks') {
            steps {
                script {
                    if (fileExists('build.gradle')) {
                        sh './gradlew check'
                    } else if (fileExists('pom.xml')) {
                        sh 'mvn sonar:sonar'
                    } else {
                        error 'No build file found!'
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'master') {
                        // Deploy to production
                        sh './deploy.sh production'
                    } else {
                        // Deploy to staging
                        sh './deploy.sh staging'
                    }
                }
            }
        }

        stage('Notify') {
            steps {
                script {
                    if (currentBuild.result == 'SUCCESS') {
                        mail to: 'team@example.com',
                             subject: "Build ${currentBuild.fullDisplayName} completed successfully",
                             body: "Build ${currentBuild.fullDisplayName} completed successfully."
                    } else {
                        mail to: 'team@example.com',
                             subject: "Build ${currentBuild.fullDisplayName} failed",
                             body: "Build ${currentBuild.fullDisplayName} failed. Please check Jenkins for details."
                    }
                }
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: '**/target/*.jar', allowEmptyArchive: true
            junit '**/target/surefire-reports/*.xml'
        }

        success {
            echo 'Pipeline completed successfully!'
        }

        failure {
            echo 'Pipeline failed. Check logs for details.'
        }
    }
}
