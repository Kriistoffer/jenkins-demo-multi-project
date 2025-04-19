pipeline {
    agent any

    stages {
        stage("TESTING") {
            steps {
                script {
                    def files = findFiles(glob: '**/appsettings.json')
                    echo "Path: ${files[0].path}"
                }
            }
        } 
    }
}