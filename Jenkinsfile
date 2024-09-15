pipeline {
    agent any
    tools {
        nodejs "nodejs"
        dotnetsdk "dotnet"
    }
    environment {
        node_projects = "frontend-gui,frontend-components,frontend-admin"
        dotnet_projects = "jenkins-demo-backend"
    }
    stages {
        stage("Build node based repositories") {
            steps {
                script {
                    echo "Building..."
                    env.node_projects.tokenize(",").each { project ->
                        echo "Installing ${project} now..."
                        sh "mkdir -p ${project}"
                        dir("${project}") {
                            sh "npm install"
                        }
                        echo "Finished installing ${project}."
                        sh "dotnet"
                    }
                }
            }
        }
        stage("Node: version and audit check") {
            steps {
                echo "Auditing and checking dependency versions..."
                sh "mkdir -p logs/${BUILD_NUMBER}"
                script {
                    env.node_projects.tokenize(",").each { project ->
                        echo "Checking ${project}..."

                        dir("${project}") {
                            sh "npm outdated --json > ../logs/${BUILD_NUMBER}/${project}_outdated_dependencies.json || true"
                            def outdated_output = readJSON(file: "../logs/${BUILD_NUMBER}/${project}_outdated_dependencies.json")

                            //Format testing
                            sh "npm outdated > ../logs/${BUILD_NUMBER}/${project}_outdated_dependencies.txt || true"
                            sh "npm audit > ../logs/${BUILD_NUMBER}/${project}_vulnerabilities.txt || true"
                            
                            sh "npm audit --json > ../logs/${BUILD_NUMBER}/${project}_vulnerabilities.json || true"
                            def audit_output = readJSON(file: "../logs/${BUILD_NUMBER}/${project}_vulnerabilities.json")

                            slackSend(channel: "#team1-dependency_check", message: "- ${project} - Outdated dependencies: ${outdated_output.size()}. Vulnerabilities found: ${audit_output.metadata.vulnerabilities.total}")
                        }
                    }
                }
            }
        }
    }
    post {
        success {
            script {
                def now = new Date()
                slackSend(channel: "#team1-dependency_check", color: "good", message: "${JOB_NAME} has finished running at ${now.format("yyMMdd-HH:mm", TimeZone.getTimeZone("GMT+2"))}. Logs available at ${BUILD_URL}execution/node/3/ws/logs/${BUILD_NUMBER}")
            }
        }
        failure {
            echo "FAILURE"
            // slackSend(channel: "#team1-dependency_check", color: "bad", message: "Something has caused a failure when running ${JOB_NAME} at ${now.format("yyMMdd-HH:mm", TimeZone.getTimeZone("GMT+2"))}. The failed build can be found here: ${BUILD_URL}")
        }
        always {
            // cleanWs()
            echo "Finished running."
        }
    }

}