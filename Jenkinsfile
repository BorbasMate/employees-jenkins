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
        stage('Set up MariaDB') {
            steps {
                script {
                    // Start MariaDB container
                    docker.image('mariadb:11.4.2').withRun('-e MARIADB_USER=root -e MARIADB_PASSWORD=test1234 -e MARIADB_DATABASE=employees -p 3306:3306') 
                        // Wait for the database to start
                        sleep 30 // adjust if needed for the database to be ready
                        
                        // Run the rest of the pipeline
                        script {
                            // Set the DB URL for subsequent stages
                            env.DB_URL = "jdbc:mariadb://localhost:3306/employees"
                        }
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