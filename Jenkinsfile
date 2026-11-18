
pipeline {
    agent any

    tools {
        jdk 'JDK_26'
        maven 'MAVEN_3_9'
    }

    environment {
        IMAGE_NAME = 'retail-store-practica'
        IMAGE_TAG = '1.0'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build & Test') {
            steps {
                sh 'mvn -B clean verify'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('MiSonarServer') {
                    sh '''
                        mvn -B sonar:sonar \
                          -Dsonar.projectKey=deisw-retail-store \
                          -Dsonar.projectName=deisw-retail-store
                    '''
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                    docker build \
                      -t "$IMAGE_NAME:$IMAGE_TAG" .
                '''
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'DOCKER_HUB_CREDENTIALS',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_TOKEN'
                    )
                ]) {
                    sh '''
                        set +x

                        export DOCKER_CONFIG="$(mktemp -d)"
                        trap 'rm -rf "$DOCKER_CONFIG"' EXIT

                        printf '%s' "$DOCKER_TOKEN" |
                          docker login \
                            --username "$DOCKER_USER" \
                            --password-stdin

                        docker tag \
                          "$IMAGE_NAME:$IMAGE_TAG" \
                          "$DOCKER_USER/$IMAGE_NAME:$IMAGE_TAG"

                        docker push \
                          "$DOCKER_USER/$IMAGE_NAME:$IMAGE_TAG"
                    '''
                }
            }
        }
    }
}

