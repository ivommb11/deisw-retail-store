
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
    }
}

