pipeline {
    agent {
        label 'Build-agent'
    } 
     
    tools {
        maven 'Maven 3.8.5'   // Adjust based on Jenkins configuration
    }
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/itundret/Angular-7-Spring-boot-2.1-microservices-PostgreSQL-10.git'
            }
        }
        stage('Build Backend Services') {
            steps {
                script {
                    def services = ['faculty', 'spring-cloud-config', 'eureka', 'zuul']
                    services.each { service ->
                        dir(service) {
                            echo "Building ${service}..."
                            sh 'mvn clean install -DskipTests'
                        }
                    }
                }
            }
        }

        stage('Build Angular Frontend') {
            steps {
                dir('angular-frontend') {
                    sh 'npm install'
                    sh 'ng build --prod'
                }
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: '**/target/*.jar', allowEmptyArchive: true
            }
        }

    }

    post {
        always {
            echo 'Pipeline finished.'
        }
        success {
            echo 'Build completed successfully!'
        }
        failure {
            echo 'Build failed!'
        }
    }
}
