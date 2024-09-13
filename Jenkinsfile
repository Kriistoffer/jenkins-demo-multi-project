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

        // stage("Build dotnet repositories") {
        //     steps {
        //         echo "Building..."
        //         script {
        //             env.dotnet_repositories.tokenize(",").each { dotnet ->
        //                 echo "Starting with repository ${dotnet} now..."
        //                 sh "mkdir -p ${dotnet}"
        //                 dir("${dotnet}") {
        //                     sh "nuget install packages.config"
        //                 }
        //             }
        //         }
        //     }
        // }
        
        stage("Dependency version check") {
            steps {
                echo "Checking versions..."
                script {
                    env.node_repositories.tokenize(",").each { npm -> 
                        echo "Checking ${npm}..."
                        dir("${npm}") {
                            sh "npm outdated || true" 
                        }
                    }
                }
            }
        }

        stage("Audit check") {
            steps {
                echo "Auditing repositories..."
                script {
                    env.node_repositories.tokenize(",").each { npm -> 
                        echo "Auditing ${npm}..."
                        dir("${npm}") {
                            sh "npm audit --json > ../npm_audit_${npm}.json || true" 
                            def result = readJSON(file: "../npm_audit_${npm}.json")
                            echo "Number of vulnerabilities found: ${result.metadata.vulnerabilities.total} (${result.metadata.vulnerabilities.critical} critical, ${result.metadata.vulnerabilities.high} high, ${result.metadata.vulnerabilities.moderate} moderate, ${result.metadata.vulnerabilities.low} low, and ${result.metadata.vulnerabilities.info} info)."
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