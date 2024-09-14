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
                echo "Building..."
                script {
                    env.node_projects.tokenize(",").each { npm ->
                        echo "Installing ${npm} now..."
                        sh "mkdir -p ${npm}"
                        dir("${npm}") {
                            sh "npm install"
                        }
                        echo "Finished installing ${npm}."
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
                            sh "npm outdated --json > ../logs/${BUILD_NUMBER}/npm_outdated_${project}.json || true"
                            def outdated_output = readJSON(file: "../logs/${BUILD_NUMBER}/npm_outdated_${project}.json")
                            sh "npm audit --json > ../npm_audit_${project}.json || true"
                            def audit_output = readJSON(file: "../npm_audit_${project}.json")
                            slackSend(channel: "#team1-dependency_check", message: "- ${project} - Outdated dependencies: ${outdated_output.size()}")
                            slackSend(channel: "#team1-dependency_check", message: "- ${project} - Vulnerabilities found: ${audit_output.metadata.vulnerabilities.total}")
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
        // stage("Node: dependency version check") {
        //     steps {
        //         echo "Checking versions..."
        //         script {
        //             env.node_projects.tokenize(",").each { npm ->
        //                 echo "Checking ${npm}..."
        //                 dir("${npm}") {
        //                    sh "npm outdated --json > ../npm_outdated_${npm}.json || true"
        //                    def result = readJSON(file: "../npm_outdated_${npm}.json")
        //                    slackSend(channel: "#team1-dependency_check", message: "- ${npm} - Outdated dependencies: ${result.size()}")
        //                    slackSend(channel: "#team1-dependency_check", message: "Number of outdated dependencies found in project ${npm}: ${result.size()}.")
        //                 }
        //             }
        //         }
        //     }
        // }
        // stage("Node: audit check") {
        //     steps {
        //         echo "Auditing repositories..."
        //         script {
        //             env.node_projects.tokenize(",").each { npm ->
        //                 echo "Auditing ${npm}..."
        //                 dir("${npm}") {
        //                     sh "npm audit --json > ../npm_audit_${npm}.json || true"
        //                     def result = readJSON(file: "../npm_audit_${npm}.json")
        //                     slackSend(channel: "#team1-dependency_check", color: "good", message: "- ${npm} - ${result.metadata.vulnerabilities.total} vulnerabilities found (${result.metadata.vulnerabilities.critical} critical, ${result.metadata.vulnerabilities.high} high, ${result.metadata.vulnerabilities.moderate} moderate, ${result.metadata.vulnerabilities.low} low, and ${result.metadata.vulnerabilities.info} info).")
        //                 }
        //             }
        //         }
        //     }
        // }
    }
    post {
        always {
            cleanWs(patterns: [[pattern: "**/logs/**", type: 'EXCLUDE']])
        }
    }

}