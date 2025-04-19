pipeline {
    agent any

    stages {
        stage("TESTING") {
            steps {
                script {
                    def files = findFiles(glob: '**/package-lock.json')
                    echo "Files: ${files}"
                }
            }
        } 
    }
}