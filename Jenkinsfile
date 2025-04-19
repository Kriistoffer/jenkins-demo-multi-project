pipeline {
    agent any

    stages {
        stage("TESTING") {
            steps {
                script {
                    def files = findFiles(glob: '**/package-lock.json')
                    echo "Files: ${files}"

                    for (file in files) {
                        parentDirectory = "/${file.path}" - "/${file.name}"
                        echo "Parent directory: ${parentDirectory}"
                        echo "Name: ${file.name}"
                    }
                }
            }
        } 
    }
}