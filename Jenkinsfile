@Library('my-shared-library') _

pipeline {

    agent any

    // IMPORTANT: avoid default SCM issues
    options {
        skipDefaultCheckout(true)
    }

    parameters {
        // FIXED: correct choice format
        choice(name: 'action', choices: ['create', 'delete'], description: 'Choose create/Destroy')
        string(name: 'ImageName', defaultValue: 'javapp', description: 'Docker image name')
        string(name: 'ImageTag', defaultValue: 'v1', description: 'Docker image tag')
        string(name: 'DockerHubUser', defaultValue: 'gopikrishna05', description: 'DockerHub username')
    }

    stages {

        // DEBUG STAGE (VERY IMPORTANT)
        stage('DEBUG') {
            steps {
                echo "Selected action is: ${params.action}"
            }
        }

        stage('Git Checkout') {
            when { expression { params.action == 'create' } }
            steps {
                gitCheckout(
                    branch: "main",
                    // FIXED: YOUR repo (not others)
                    url: "https://github.com/Gopikrishna-05/JAVA-APPLICATION-3.0.git"
                )
            }
        }

        stage('Unit Test') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    mvnTest()
                }
            }
        }

        stage('Integration Test') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    mvnIntegrationTest()
                }
            }
        }

        stage('SonarQube Analysis') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    def sonarId = 'sonarqube-api'
                    statiCodeAnalysis(sonarId)
                }
            }
        }

        stage('Quality Gate') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    def sonarId = 'sonarqube-api'
                    QualityGateStatus(sonarId)
                }
            }
        }

        stage('Maven Build') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    mvnBuild()
                }
            }
        }

        stage('Docker Build') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    dockerBuild(params.ImageName, params.ImageTag, params.DockerHubUser)
                }
            }
        }

        stage('Docker Scan') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    dockerImageScan(params.ImageName, params.ImageTag, params.DockerHubUser)
                }
            }
        }

        stage('Docker Push') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    dockerImagePush(params.ImageName, params.ImageTag, params.DockerHubUser)
                }
            }
        }

        stage('Docker Cleanup') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    dockerImageCleanup(params.ImageName, params.ImageTag, params.DockerHubUser)
                }
            }
        }
    }
}
