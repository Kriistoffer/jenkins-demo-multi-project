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
                script {
                    env.node_repositories.tokenize(",").each { npm ->
                        echo "Starting with repository ${npm} now..."
                        sh "mkdir -p ${npm}"
                        sh "cd ${npm}"
                        sh "npm install"
                        sh "cd .."
                        echo "Finished building ${npm}."
                    }
                }
            }
        }
    }
    post {
        always {
            cleanWs(cleanWhenNotBuilt: false,
                    deleteDirs: true,
                    disableDeferredWipeout: true,
                    notFailBuild: true,
                    patterns: [[pattern: '.gitignore', type: 'INCLUDE'],
                               [pattern: '.propsfile', type: 'EXCLUDE']])
        }
    }
        
}