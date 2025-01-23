pipeline {
    agent any
    tools {
        nodejs 'NodeJS_20' // Ensure Node.js is installed on the Windows machine and configured in Jenkins
    }
    environment {
        EC2_IP = '44.204.193.1'
        EC2_USER = 'ubuntu'
    }
    stages {
        stage('Checkout') {
            steps {
                // Checkout the repository
                git branch: 'main', url: 'https://github.com/NuriIT/To-Do.git'
            }
        }
        stage('Build') {
            steps {
                // Use bat for Windows commands
                bat '''
                cd todo
                npm install
                npm run build
                '''
            }
        }
        stage('Deploy') {
            steps {
                sshagent(['credential-id']) {
                    // Add the EC2 host key to known_hosts (Windows PowerShell command)
                    bat """
                    echo | ssh-keyscan -H ${EC2_IP} >> %USERPROFILE%\\.ssh\\known_hosts
                    """

                    // Copy files to the remote server using SCP (via Windows PowerShell)
                    bat """
                    powershell -Command "scp -r todo\\build ${EC2_USER}@${EC2_IP}:/home/${EC2_USER}/react-app"
                    """

                    // Deploy React app and restart Nginx on the remote server
                    bat """
                    powershell -Command "ssh ${EC2_USER}@${EC2_IP} 'sudo rm -rf /var/www/html/* && sudo cp -r /home/${EC2_USER}/react-app/* /var/www/html/ && sudo systemctl restart nginx'"
                    """
                }
            }
        }
    }
}