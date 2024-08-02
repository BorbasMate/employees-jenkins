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
        DB_URL = 'jdbc:mariadb://mariadb:3306/employees' //Jenkins uses the service name as domain
    }
    services {
        mariadb {
            image 'mariadb:11.4.2'
            env {
                MARIADB_USER = 'root'
                MARIADB_PASSWORD = 'test1234'
                MARIADB_DATABASE = 'employees'
            }
            ports {
                '3306:3306'
            }
        }
    }
    stages {
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