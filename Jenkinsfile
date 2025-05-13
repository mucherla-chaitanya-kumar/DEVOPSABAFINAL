pipeline {
    agent any

    tools {
        jdk 'jdk-17'      
        dockerTool 'docker'// Make sure 'jdk-17' is defined in Jenkins > Global Tool Configuration
    }

    environment {
        DOCKER_IMAGE = 'sameekshasunil/aba'
        DOCKER_TAG = "${BUILD_NUMBER}"
        DOCKER_CLI_PATH = 'C:\\Program Files\\Docker\\Docker\\resources\\bin'
        EMAIL_RECIPIENT = 'sameeksha.s017@gmail.com'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
            post {
                always {
                    script {
                        emailext (
                            subject: "Stage Checkout Status: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                            body: """<p>Stage 'Checkout' finished.</p>
                                     <p>Check console output at <a href='${env.BUILD_URL}'>${env.BUILD_URL}</a></p>""",
                            mimeType: 'text/html',
                            to: "${env.EMAIL_RECIPIENT}"
                        )
                    }
                }
            }
        }

        stage('Build') {
            steps {
                bat '''
                    echo "JAVA_HOME: %JAVA_HOME%"
                    mvn -version
                    mvn clean package
                '''
            }
            post {
                always {
                    script {
                        emailext (
                            subject: "Stage Build Status: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                            body: """<p>Stage 'Build' completed.</p>
                                     <p>Check console output at <a href='${env.BUILD_URL}'>${env.BUILD_URL}</a></p>""",
                            mimeType: 'text/html',
                            to: "${env.EMAIL_RECIPIENT}"
                        )
                    }
                }
            }
        }

        stage('Test') {
            steps {
                bat 'mvn test'
            }
            post {
                always {
                    script {
                        emailext (
                            subject: "Stage Test Status: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                            body: """<p>Stage 'Test' completed.</p>
                                     <p>Check console output at <a href='${env.BUILD_URL}'>${env.BUILD_URL}</a></p>""",
                            mimeType: 'text/html',
                            to: "${env.EMAIL_RECIPIENT}"
                        )
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                bat """
                    set PATH=${env.DOCKER_CLI_PATH};%PATH%
                    docker build -t ${env.DOCKER_IMAGE}:${env.DOCKER_TAG} .
                    docker tag ${env.DOCKER_IMAGE}:${env.DOCKER_TAG} ${env.DOCKER_IMAGE}:latest
                """
            }
            post {
                always {
                    script {
                        emailext (
                            subject: "Stage Docker Build Status: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                            body: """<p>Stage 'Build Docker Image' completed.</p>
                                     <p>Check console output at <a href='${env.BUILD_URL}'>${env.BUILD_URL}</a></p>""",
                            mimeType: 'text/html',
                            to: "${env.EMAIL_RECIPIENT}"
                        )
                    }
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    bat """
                        set PATH=${env.DOCKER_CLI_PATH};%PATH%
                        echo Logging into Docker Hub...
                        echo %DOCKER_PASSWORD% | docker login -u %DOCKER_USERNAME% --password-stdin
                        docker push ${env.DOCKER_IMAGE}:${env.DOCKER_TAG}
                        docker push ${env.DOCKER_IMAGE}:latest
                        docker logout
                    """
                }
            }
            post {
                always {
                    script {
                        emailext (
                            subject: "Stage Docker Push Status: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                            body: """<p>Stage 'Push to Docker Hub' completed.</p>
                                     <p>Check console output at <a href='${env.BUILD_URL}'>${env.BUILD_URL}</a></p>""",
                            mimeType: 'text/html',
                            to: "${env.EMAIL_RECIPIENT}"
                        )
                    }
                }
            }
        }
    }

    post {
        always {
            script {
                emailext (
                    subject: "Pipeline Status: ${env.JOB_NAME} #${env.BUILD_NUMBER} - ${currentBuild.result ?: 'UNKNOWN'}",
                    body: """<p>Pipeline completed with status: ${currentBuild.result ?: 'UNKNOWN'}</p>
                             <p>View details: <a href='${env.BUILD_URL}'>${env.BUILD_URL}</a></p>""",
                    mimeType: 'text/html',
                    to: "${env.EMAIL_RECIPIENT}"
                )
            }
        }
    }
}
