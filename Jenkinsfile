pipeline {
    agent any
    tools {
            jdk "java-1.8"
            maven "Maven-3.5.3"
        }
    stages {
        stage ('Clone') {
            steps {
                git url: 'https://github.com/kohbah/simple-java-maven-app.git'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Deliver') {
            steps {
                sh './scripts/deliver.sh'
            }
        }
    }
}
