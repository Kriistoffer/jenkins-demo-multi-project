pipeline {
    agent any 
    tools {
        nodejs "nodejs"
    }
    environment {
        node_repositories = 'frontend-gui,frontend-components'
        dotnet_repositories = 'jenkins-demo-backend'
    }
    stages {
        stage('Build node repositories') {
            steps {
                echo 'Building...'
                // sh 'mkdir -p frontend-gui'
                // sh 'pwd'
                // dir('frontend-gui') {
                //     sh 'pwd'
                // }
                // sh 'pwd'
                script {
                    env.node_repositories.tokenize(",").each { npm ->
                        echo "Starting with repository ${npm} now..."
                        sh "mkdir -p ${npm}"
                        dir("${npm}") {
                            sh "npm install"

                        }
                        echo "Finished building ${npm}."
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