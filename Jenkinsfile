pipeline {
    agent any 
    tools {
        nodejs "nodejs"
        dotnetsdk "dotnet"
    }
    environment {
        node_repositories = "frontend-gui,frontend-components"
        dotnet_repositories = "jenkins-demo-backend"
    }
    stages {
        stage("Build node repositories") {
            steps {
                echo "Building..."
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
        stage("Build dotnet repositories") {
            steps {
                echo "Building..."
                script {
                    env.dotnet_repositories.tokenize(",").each { dotnet ->
                        echo "Starting with repository ${dotnet} now..."
                        sh "mkdir -p ${dotnet}"
                        dir("${dotnet}") {
                            bat "nuget install packages.config"
                        }
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