pipeline {
    agent any

    stages {
        stage("TESTING") {
            steps {
                script {
                    def files = findFiles(glob: '**/package-lock.json')
                    echo "Files: ${files}"

                    dir("${files[0].path}") {
                        sh "pwd"
                    }
                }
            }
        } 
    }
}