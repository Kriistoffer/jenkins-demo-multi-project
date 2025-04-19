pipeline {
    agent any

    stages {
        stage("TESTING") {
            steps {
                script {
                    def files = findFiles(glob: '**/appsettings.json')
                    echo "${files[0]}"
                }
            }
        } 
    }
}