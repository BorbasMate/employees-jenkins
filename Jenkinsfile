pipeline {
    // first pipelne run with any -> but we want to run the build in java environment -> for this we created the jenkins with a docker client
    // agent any
    agent {
        docker { 
            image 'eclipse-temurin:17' 
        }
    }
    environment {
        MAVEN_OPTS = '-Dmaven.repo.local=/var/jenkins_home/.m2/repository' // Use local Maven repo
    }
    stages {
        pipeline {
    agent any
    
    environment {
        MAVEN_OPTS = '-Dmaven.repo.local=/var/jenkins_home/.m2/repository' // Use local Maven repo
    }

    stages {
        stage('Set up MariaDB') {
            steps {
                script {
                    writeFile file: 'docker-compose.yml', text: """
                    version: '3.1'
                    services:
                      mariadb:
                        image: mariadb:11.4.2
                        environment:
                          MARIADB_USER: root
                          MARIADB_PASSWORD: test1234
                          MARIADB_DATABASE: employees
                        ports:
                          - "3306:3306"
                    """
                    sh 'docker-compose up -d'
                    // Wait for the database to be ready
                    sleep 30 // adjust the sleep time if necessary
                }
            }
        }
        stage('Commit') {
            steps {
                echo "Commit stage"
                // original code before add of version number
                // sh "./mvnw -B package"  //-B flag makes this readable in Jenkins - no colors used
                sh "./mvnw -B package -Dbuild.number=${BUILD_NUMBER}"
            }
        } 
        stage('Acceptance') {
            steps {
                echo "Acceptance stage"
                sh "./mvnw -B integration-test -Dbuild.number=${BUILD_NUMBER}"
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