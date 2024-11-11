pipeline {
    agent any
    environment {
        IMAGE_NAME = "elshabahmed/uptime:latest"
    }
    stages {
        
        stage("Git Pull") {
            steps {
                git branch: 'main', url: 'https://github.com/elshabahmed/CI-CD-Pipeline-for-Uptime-Kuma-Get-Real-Time-Call-Alerts-for-Server-Downtime-Open-Source-Monitoring.git'
            }
        }
        
        stage('Update Kubernetes Manifests') {
            steps {
                script {
                    // Clone the Git repo
                    git url: 'https://github.com/elshabahmed/CI-CD-Pipeline-for-Uptime-Kuma-Get-Real-Time-Call-Alerts-for-Server-Downtime-Open-Source-Monitoring.git', branch: 'main'
                    
                    // Update the Kubernetes manifest with the new image
                    sh """
                    sed -i 's|image: .*|image: elshabahmed/uptime:latest|' k8s/app.yml
                    """
                    
                    // Use Jenkins credentials for Git push
                    withCredentials([usernamePassword(credentialsId: 'git-hub', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                        sh '''
                        git config user.name "$GIT_USERNAME"
                        git config user.email "elshab.ahmed2000@gmail.com"
                        git remote set-url origin https://$GIT_USERNAME:$GIT_PASSWORD@github.com/elshabahmed/CI-CD-Pipeline-for-Uptime-Kuma-Get-Real-Time-Call-Alerts-for-Server-Downtime-Open-Source-Monitoring.git
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
