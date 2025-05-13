pipeline {
    agent any

    tools {
        jdk 'jdk-17'               // Set this name in Jenkins > Global Tool Configuration
        dockerTool 'docker'
    }

    environment {
        DOCKER_IMAGE = 'sathwikpadmanabha/devopsfinal'
        DOCKER_TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
            post {
                always {
                    emailext (
                        subject: "Stage Checkout Status: ${currentBuild.fullDisplayName}",
                        body: """<p>Stage 'Checkout' finished with status: ${currentBuild.result}</p>
                                 <p>Check console output at <a href='${BUILD_URL}'>${BUILD_URL}</a></p>""",
                        mimeType: 'text/html',
                        to: 'sameeksha.s017@gmail.com'
                    )
                }
            }
        }

        stage('Build') {
            steps {
                bat '''
                    echo "JAVA_HOME: %JAVA_HOME%"
                    echo "Maven version:"
                    mvn --version
                    echo "Building project..."
                    mvn clean package
                '''
            }
            post {
                always {
                    emailext (
                        subject: "Stage Build Status: ${currentBuild.fullDisplayName}",
                        body: """<p>Stage 'Build' finished with status: ${currentBuild.result}</p>
                                 <p>Check console output at <a href='${BUILD_URL}'>${BUILD_URL}</a></p>""",
                        mimeType: 'text/html',
                        to: 'sameeksha.s017@gmail.com'
                    )
                }
            }
        }

        stage('Test') {
            steps {
                bat '''
                    echo "Running tests..."
                    mvn test
                '''
            }
            post {
                always {
                    emailext (
                        subject: "Stage Test Status: ${currentBuild.fullDisplayName}",
                        body: """<p>Stage 'Test' finished with status: ${currentBuild.result}</p>
                                 <p>Check console output at <a href='${BUILD_URL}'>${BUILD_URL}</a></p>""",
                        mimeType: 'text/html',
                        to: 'sameeksha.s017@gmail.com'
                    )
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                bat """
                    set PATH=C:\\Program Files\\Docker\\Docker\\resources\\bin;%PATH%
                    docker build -t ${env.DOCKER_IMAGE}:${env.DOCKER_TAG} .
                    docker tag ${env.DOCKER_IMAGE}:${env.DOCKER_TAG} ${env.DOCKER_IMAGE}:latest
                """
            }
            post {
                always {
                    emailext (
                        subject: "Stage Build Docker Image Status: ${currentBuild.fullDisplayName}",
                        body: """<p>Stage 'Build Docker Image' finished with status: ${currentBuild.result}</p>
                                 <p>Check console output at <a href='${BUILD_URL}'>${BUILD_URL}</a></p>""",
                        mimeType: 'text/html',
                        to: 'sameeksha.s017@gmail.com'
                    )
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    bat """
                        set PATH=C:\\Program Files\\Docker\\Docker\\resources\\bin;%PATH%
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
                    emailext (
                        subject: "Stage Push to Docker Hub Status: ${currentBuild.fullDisplayName}",
                        body: """<p>Stage 'Push to Docker Hub' finished with status: ${currentBuild.result}</p>
                                 <p>Check console output at <a href='${BUILD_URL}'>${BUILD_URL}</a></p>""",
                        mimeType: 'text/html',
                        to: 'sameeksha.s017@gmail.com'
                    )
                }
            }
        }
    }

    post {
        always {
            emailext (
                subject: "Pipeline Status: ${currentBuild.fullDisplayName}",
                body: """<p>Pipeline Status: ${currentBuild.result}</p>
                         <p>Check console output at <a href='${BUILD_URL}'>${BUILD_URL}</a></p>""",
                mimeType: 'text/html',
                to: 'sameeksha.s017@gmail.com'
            )
        }
    }
}
