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
                def now = new Date()
                slackSend(channel: "#team1-dependency_check", message: "${JOB_NAME} has begun running at ${now.format("yyMMdd-HH:mm", TimeZone.getTimeZone("GMT+2"))}.")
                echo "Building..."

                script {
                    env.node_projects.tokenize(",").each { project ->
                        echo "Installing ${project} now..."
                        sh "mkdir -p ${project}"
                        dir("${project}") {
                            sh "npm install"
                        }
                        echo "Finished installing ${project}."
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
                            //Outdated check
                            sh "npm outdated --json > ../logs/${BUILD_NUMBER}/${project}_outdated_dependencies.json || true"
                            def outdated_output = readJSON(file: "../logs/${BUILD_NUMBER}/${project}_outdated_dependencies.json")

                            //Format testing
                            sh "npm outdated > ../logs/${BUILD_NUMBER}/${project}_outdated_dependencies.txt || true"
                            sh "npm audit > ../logs/${BUILD_NUMBER}/${project}_vulnerabilities.txt || true"
                            
                            //Audit check
                            sh "npm audit --json > ../logs/${BUILD_NUMBER}/${project}_vulnerabilities.json || true"
                            def audit_output = readJSON(file: "../logs/${BUILD_NUMBER}/${project}_vulnerabilities.json")
                            //Posting all results to slack
                            slackSend(channel: "#team1-dependency_check", message: "- ${project} - Outdated dependencies: ${outdated_output.size()}. Vulnerabilities found: ${audit_output.metadata.vulnerabilities.total}")
                        }
                    }
                }
            }
        }
        // stage("Build dotnet based repositories") {
        //     steps {
        //         echo "Building..."
        //         script {
        //             env.dotnet_projects.tokenize(",").each { dotnet ->
        //                 echo "Starting with repository ${dotnet} now..."
        //                 sh "mkdir -p ${dotnet}"
        //                 dir("${dotnet}") {
        //                     sh "nuget install packages.config"
        //                 }
        //             }
        //         }
        //     }
        // }
    }
    post {
        success {
            slackSend(channel: "#team1-dependency_check", color: "good", message: "${JOB_NAME} has finished running. Logs available at ${BUILD_URL}execution/node/3/ws/logs/${BUILD_NUMBER}")
        }
        failure {
            slackSend(channel: "#team1-dependency_check", color: "bad", message: "Something has caused a failure when running ${JOB_NAME}.")
        }
        always {
            // cleanWs(patterns: [[pattern: "**/logs/**", type: 'EXCLUDE']])
            // cleanWs()
            echo "Finished running."
        }
    }

}