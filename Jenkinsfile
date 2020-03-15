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
                    // By default, 3 threads are used to upload the artifacts to Artifactory. You can override this default by setting:
                )
                rtMavenRun (
                    // Tool name from Jenkins configuration.
                    tool: maven,
                    pom: 'pom.xml',
                    goals: 'clean install',
                    // Maven options.
                    opts: '-Xms1024m -Xmx4096m',
                    resolverId: 'resolver',
                    deployerId: 'deploy',
                    // If the build name and build number are not set here, the current job name and number will be used:
                   buildName: 'my-build-name',
                   buildNumber: '17'
                )
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
                sh 'java -jar /var/jenkins_home/workspace/simple-java-maven-app/target/my-app-1.0-SNAPSHOT.jar'
            }
        }
    }
}
