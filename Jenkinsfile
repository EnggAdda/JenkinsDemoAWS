pipeline {
    agent any

    environment {
        EC2_HOST = 'ec2-18-181-182-3.ap-northeast-1.compute.amazonaws.com'
        EC2_USER = 'ec2-user'
        JAR_FILE = 'target/jenkins-demo.jar'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/EnggAdda/JenkinsDemoAWS.git'
            }
        }

        stage('Build') {
            steps {
                script {
                    // Ensure mvnw has executable permissions
                    sh 'chmod +x mvnw'
                    // Run Maven build
                    sh './mvnw clean package'
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Copy JAR file to EC2 instance using SSH credentials from Jenkins
                    sh "scp -i /dev/null -o StrictHostKeyChecking=no ${JAR_FILE} ${EC2_USER}@${EC2_HOST}:/home/${EC2_USER}/jenkins-demo.jar"

                    // SSH into EC2 instance and start application
                    sshagent(credentials: ['your-ssh-credentials-id']) {
                        sh """
                        ssh -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_HOST} 'bash -s' <<-'ENDSSH'
                        pkill -f jenkins-demo.jar || true
                        nohup java -jar /home/${EC2_USER}/jenkins-demo.jar > /dev/null 2>&1 &
                        ENDSSH
                        """
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
