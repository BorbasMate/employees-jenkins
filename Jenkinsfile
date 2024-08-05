pipeline {
    // first pipelne run with any -> but we want to run the build in java environment -> for this we created the jenkins with a docker client
    // agent any
    // second agent if docker client was not needed -> only use mvnw for build
    // agent {
    //     docker { 
    //         image 'eclipse-temurin:17' 
    //     }
    // }
    // we will build an image that contains eclipse-temurin:17 + a docker client that will talk to the docker engine on our host machine
    agent {
        dockerfile {
            filename 'Dockerfile.build'
            args '-e DOCKER_CONFIG=./docker'
        }
    }

    environment {
        MAVEN_OPTS = '-Dmaven.repo.local=/var/jenkins_home/.m2/repository' // Use local Maven repo
        DB_URL = 'jdbc:mariadb://host.docker.internal:3306/employees'
        DB_USER = 'root'
        DB_PASSWORD = 'test1234'
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
        VERSION_NUMBER = sh (
                    script: './mvnw help:evaluate -Dexpression=project.version -Dbuild.number=${BUILD_NUMBER} -q -DforceStdout',
                    returnStdout: true).trim()                
        IMAGE_NAME = "mborbas/employees:${VERSION_NUMBER}"
        SONAR_CREDENTIALS = credentials('sonar-credentials')
    }
        
    stages {
        stage('Commit') {
            steps {
                echo "Commit stage"
                // original code before add of version number
                // sh "./mvnw -B package"  //-B flag makes this readable in Jenkins - no colors used
                // second code before versioning 
                // sh "./mvnw -B package -Dbuild.number=${BUILD_NUMBER}"
                // code before creating zip report for e2e tetst -> commented out to speed up
                // sh "./mvnw -B clean package -Dbuild.number=${BUILD_NUMBER}"
            }
        } 
        stage('Acceptance') {
            steps {
                echo "Acceptance stage"
                // code before creating zip report for e2e tetst -> commented out to speed up
                // sh "./mvnw -B integration-test -Dbuild.number=${BUILD_NUMBER} \
                //     -Dtest.datasource.url=${DB_URL} \
                //     -Dtest.datasource.username=${DB_USER} \
                //     -Dtest.datasource.password=${DB_PASSWORD}"
            }
        } 
        stage('Docker') {
            steps {
                echo "Docker stage"
                // code before creating zip report for e2e tetst -> commented out to speed up
                // sh "docker build -f Dockerfile.layered -t ${IMAGE_NAME} ."
                // sh "echo ${DOCKERHUB_CREDENTIALS_PSW} | docker login -u=${DOCKERHUB_CREDENTIALS_USR} --password-stdin"
                // sh "docker push ${IMAGE_NAME}"
                // // we also push the image with latest tag, to be fully precize
                // sh "docker tag ${IMAGE_NAME} mborbas/employees:latest"
                // sh "docker push mborbas/employees:latest"                
            }
        } 
        stage('E2E API') {            
            steps {
                //start in employees-postman folder
                dir('employees-postman') {
                    sh 'rm -rf reports'
                    sh 'mkdir reports'
                    //code before sonarqube (Code quality) stage -> commented out to speed up
                    // sh 'docker compose -f docker-compose.yaml -f docker-compose.jenkins.yaml up --abort-on-container-exit'  
                    // archiveArtifacts artifacts: 'reports/*.html', fingerprint: true                  
                }
            }
        } 
        stage('Code quality') {
            steps {
                sh "./mvnw sonar:sonar -Dsonar.host.url=http://host.docker.internal:9000 -Dsonar.login=${SONAR_CREDENTIALS_PSW}"
            }
        }

    }
    post {
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed!'
        }
        always {
            echo 'Pipeline completed!'
        }
    }
}


