pipeline {
    agent any
    environment {
        IMAGE_NAME = "elshabahmed/uptime:latest"
    }
    stages {
        
        stage("Git Pull") {
            steps {
                git branch: 'main', url: 'git@github.com:elshabahmed/CI-CD-Pipeline-for-Uptime-Kuma-Get-Real-Time-Call-Alerts-for-Server-Downtime-Open-Source-Monitoring.git'
            }
        }
        
        stage('Update Kubernetes Manifests') {
            steps {
                script {
                    // Clone the Git repo using SSH
                    git url: 'git@github.com:elshabahmed/CI-CD-Pipeline-for-Uptime-Kuma-Get-Real-Time-Call-Alerts-for-Server-Downtime-Open-Source-Monitoring.git', branch: 'main'
                    
                    // Update the Kubernetes manifest with the new image
                    sh """
                    sed -i 's|image: .*|image: elshabahmed/uptime:latest|' k8s/app.yml
                    """
                    
                    // Use Jenkins SSH credentials for Git push
                    withCredentials([sshUserPrivateKey(credentialsId: 'git-ssh-key', keyFileVariable: 'SSH_KEY_PATH')]) {
                        sh '''
                        eval `ssh-agent -s`
                        ssh-add $SSH_KEY_PATH
                        git config user.name "elshabahmed"
                        git config user.email "elshab.ahmed2000@gmail.com"
                        git remote set-url origin git@github.com:elshabahmed/CI-CD-Pipeline-for-Uptime-Kuma-Get-Real-Time-Call-Alerts-for-Server-Downtime-Open-Source-Monitoring.git
                        git add k8s/app.yml
                        git commit -m "Update image to elshabahmed/uptime:latest"
                        git push origin main
                        '''
                    }
                }
            }
        }
    }
}
