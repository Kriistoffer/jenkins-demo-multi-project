pipeline {
    agent any 
    tools {
        nodejs "nodejs"
    }
    environment {
        node_repositories = ['/frontend-gui', '/frontend-components']
        dotnet_repositories = ['/jenkins-demo-backend']
    }
    stages {
        stage('Build node repositories') {
            steps {
                echo 'Building...'
                script {
                    node_repositories.each() {
                        echo 'Looping'
                    }
                }
            }
        }
        
    }
}