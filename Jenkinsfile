pipeline{
    agent any
    tools {
        jdk 'jdk17'
        nodejs 'node18'
    }
    environment {
        SCANNER_HOME=tool 'sonar'
    }
    stages {
        stage ("Git Pull"){
            steps{
                git branch: 'main', url: 'https://github.com/elshabahmed/CI-CD-Pipeline-for-Uptime-Kuma-Get-Real-Time-Call-Alerts-for-Server-Downtime-Open-Source-Monitoring.git'
            }
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install --legacy-peer-deps"
            }
        }
        stage("Sonarqube Analysis "){
            steps{
                withSonarQubeEnv('sonar') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Chatbot \
                    -Dsonar.projectKey=Chatbot '''
                }
            }
        }
        stage('OWASP FS SCAN') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'DP-Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage('TRIVY FS SCAN') {
            steps {
                sh "trivy fs . > trivyfs.json"
            }
        }
        stage("Docker Build & Push"){
            steps{
                script{
                   withDockerRegistry(credentialsId: 'docker-hub', toolName: 'Docker'){   
                       sh "docker build -t uptime ."
                       sh "docker tag uptime elshabahmed/uptime:latest "
                       sh "docker push elshabahmed/uptime:latest "
                    }
                }
            }
        }
        stage("TRIVY"){
            steps{
                sh "trivy image elshabahmed/uptime:latest > trivy.json" 
            }
        }
        stage ("Remove container") {
            steps{
                sh "docker stop uptime | true"
                sh "docker rm uptime | true"
             }
        }
        stage('Deploy to container'){
            steps{
                sh 'docker run -d --name uptime -v /var/run/docker.sock:/var/run/docker.sock -p 3001:3001 elshabahmed/uptime:latest'
            }
        }
    }
}
