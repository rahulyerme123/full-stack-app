pipeline {
    agent {
        label 'Build-agent'
    }     
    tools {
        maven 'Maven 3.8.5'   // Adjust based on Jenkins configuration
    }
      environment {
        ACR_NAME = 'microservicefullstackapp-bcckhqaadjgzc0bg.azurecr.io'        // Replace with your full ACR login server
        IMAGE_TAG = "${env.BUILD_NUMBER}"
        TENANT_ID = '8c3dad1d-b6bc-4f8b-939b-8263372eced6'                // Replace with your Azure Tenant ID
    }
    stages {
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
                dir('frontend') {
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
        stage('Login to Azure & ACR') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'azure-sp', usernameVariable: 'AZ_CLIENT_ID', passwordVariable: 'AZ_CLIENT_SECRET')]) {
                    sh '''
                        echo "Logging in to Azure..."
                        az login --service-principal --username "$AZ_CLIENT_ID" --password "$AZ_CLIENT_SECRET" --tenant "$TENANT_ID"
                        
                        echo "Logging into ACR..."
                        ACR_SHORT_NAME=$(echo $ACR_NAME | cut -d'.' -f1)
                        az acr login --name $ACR_SHORT_NAME
                    '''
                }
            }
        }
        stage('Build & Push Docker Images') {
            steps {
                script {
                    def services = ['faculty', 'spring-cloud-config', 'eureka', 'zuul']
                    services.each { service ->
                        def localImage = "${service}:1.0"
                        def acrImage = "${ACR_NAME}/${service}:${IMAGE_TAG}"

                        echo "Tagging ${localImage} as ${acrImage}"
                        sh "docker tag ${localImage} ${acrImage}"

                        echo "Pushing ${acrImage} to ACR..."
                        sh "docker push ${acrImage}"
                        }
                    }
                }
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

