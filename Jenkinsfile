pipeline {
    agent any
    tools {
        maven 'maven'
        jdk 'java'
    }
    stages {
        stage ('Clone') {
            steps {
                git url: 'https://github.com/kohbah/simple-java-maven-app.git'
            }
        }       
        stage("build & SonarQube analysis") {
            agent any
            steps {
              withSonarQubeEnv('sonar') {
                sh 'mvn clean package sonar:sonar'
              }
            }
          }
          stage("Quality Gate") {
            steps {
              timeout(time: 1, unit: 'HOURS') {
                waitForQualityGate abortPipeline: true
              }
            }
          }
        stage ('Artifactory configuration') {
            steps {
               rtServer (
                 id: "jfrog",
                 url: jfrog,
                 credentialsId: jfrog
                )
                rtMavenResolver (
                    id: "resolver",
                    serverId: "jfrog",
                    releaseRepo: "libs-release",
                    snapshotRepo: "libs-snapshot"
                )
                rtMavenDeployer (
                    id: 'deploy',
                    serverId: 'jfrog',
                    releaseRepo: 'libs-release-local',
                    snapshotRepo: 'libs-snapshot-local',
                )
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
                sh 'java -jar /var/jenkins_home/workspace/simple-java-maven-app/target/my-app-1.0-SNAPSHOT.jar'
            }
        }
    }
}
