
pipeline {
    agent any

    tools {
        jdk 'JDK_26'
        maven 'MAVEN_3_9'
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
    }
}

