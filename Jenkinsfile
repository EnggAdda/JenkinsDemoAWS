pipeline {
    agent any

    environment {
        EC2_HOST = 'ec2-18-181-182-3.ap-northeast-1.compute.amazonaws.com'
        EC2_USER = 'ec2-user'
        SSH_CREDENTIALS_ID = 'ec2-ssh-key'
        APP_NAME = 'jenkins-demo'
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
                sh 'chmod +x mvnw'
                sh './mvnw clean package'
            }
        }

        stage('Deploy') {
            steps {
                sshagent(credentials: [SSH_CREDENTIALS_ID]) {
                    sh """
                    scp -o StrictHostKeyChecking=no ${JAR_FILE} ${EC2_USER}@${EC2_HOST}:/home/${EC2_USER}/${APP_NAME}.jar
                    ssh -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_HOST} 'bash -s' <<-'ENDSSH'
                    pkill -f ${APP_NAME}.jar || true
                    nohup java -jar /home/${EC2_USER}/${APP_NAME}.jar > /dev/null 2>&1 &
                    ENDSSH
                    """
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
