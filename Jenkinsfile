pipeline {
    // first pipelne run with any -> but we want to run the build in java environment -> for this we created the jenkins with a docker client
    // agent any
    agent {
        docker { 
            image 'eclipse-temurin:17' 
            args '-v /var/jenkins_home/.m2:/root/.m2' // Mount the Maven repository
        }
    }
    environment {
        MAVEN_OPTS = '-Dmaven.repo.local=/root/.m2/repository' // Use local Maven repo
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