pipeline {
    // first pipelne run with any -> but we want to run the build in java environment -> for this we created the jenkins with a docker client
    // agent any
    agent {
        docker { 
            image 'eclipse-temurin:17' 
        }
    }
    stages {
        stage('Commit') {
            steps {
                echo "Commit stage"
                sh "./mvnw -B package"  //-B flag makes this readable in Jenkins
            }
        } 
        stage('Acceptance') {
            steps {
                echo "Acceptance stage"
            }
        }   
    }
}