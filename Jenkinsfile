
pipeline {
    agent any
    environment{
        SONAR_HOME = tool "Sonar"
    }

    stages {
        stage('Code Clone') {
            steps {
                git url: 'https://github.com/ajay-mall13/deploy-chatapp-k8s.git', branch: 'master'
            }
        }
        
        
stage('GitLeaks Scan') {
    steps {
        sh '''
          gitleaks detect --source=. --report-path=gitleaks-report.json --report-format=json || true
        '''
    }
    post {
        always {
            archiveArtifacts artifacts: 'gitleaks-report.json', onlyIfSuccessful: false
        }
    }
}
        
        stage('sonar qube quality analysis'){
            steps{
                withSonarQubeEnv("Sonar"){
                   sh "$SONAR_HOME/bin/sonar-scanner -Dsonar.projectName=chat-app -Dsonar.projectKey=chat-app -X"
                }
            }
        }
      
      
            stage('OWASP Dependency Check') {
            steps {
                dependencyCheck additionalArguments: '--scan ./' , odcInstallation: 'dc'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'

                    }
        }
        
        
        stage("Trivy File System Scan") {
    steps {
        sh "trivy fs --format table -o trivy-fs-report.html ."
    }
}

stage("Sonar Quality Gate Scan") {
    steps {
        timeout(time: 2, unit: "MINUTES") {
            waitForQualityGate abortPipeline: false
        }
    }
}





      
 stages {
        stage('Frontend Build') {
            steps {
                dir('frontend') {  
                    sh 'docker build -t ${DOCKER_HUB_USER}/chatapp-frontend:latest ./frontend'
                }
            }
        }

        stage('Frontend Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhubcreds',
                    usernameVariable: 'dockerHubUser',
                    passwordVariable: 'dockerHubPass'
                )]) {
                   sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                    sh "docker image tag chatapp ${env.dockerHubUser}/chatapp:latest"
                    sh "docker push ${env.dockerHubUser}/chatapp:latest"
                }
            }
        }

        stage('Backend Build') {
            steps {
                dir('backend') {  
                    sh 'docker build -t ${DOCKER_HUB_USER}/chatapp-backend:latest ./backend'
                }
            }
        }

        stage('Backend Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhubcreds',
                    usernameVariable: 'dockerHubUser',
                    passwordVariable: 'dockerHubPass'
                )]) {
                    sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                    sh "docker image tag chatapp ${env.dockerHubUser}/chatapp:latest"
                    sh "docker push ${env.dockerHubUser}/chatapp:latest"
                }
            }
        }
    }







      

        stage('test') {
            steps {
   		echo "devloper test dega"
            }
        }
stage('Docker Deploy') {
    steps {
        sh '''
            docker-compose down || true
            docker-compose up -d --force-recreate
        '''
    }
}
    }

  post{
        success{
            script{
                emailext from: 'ajayup8082@gmail.com',
                to: 'ajayup8082@gmail.com',
                body: 'Build success for Demo CICD App',
                subject: 'Build success for Demo CICD App'
            }
        }
        failure{
            script{
                emailext from: 'ajayup8082@gmail.com',
                to: 'ajayup8082@gmail.com',
                body: 'Build Failed for Demo CICD App',
                subject: 'Build Failed for Demo CICD App'
            }
        }
    }


}
                                                                                                                                                                                           
