pipeline{
    agent{
        label "jenkins-agent"
    }
    tools {
        jdk 'Java20'
        maven 'Maven3'
    }
    environment {
        APP_NAME = "complete-prodcution-e2e-pipeline"
        RELEASE = "1.0.0"
        DOCKER_USER = "nadir24950@gmail.com"
        DOCKER_PASS = 'dockerhub'
        IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD-NUMBER}" 
    } 
    
    stages{
        stage("Cleanup Workspace"){
            steps {
                cleanWs()
            }

        }
    
        stage("Checkout from SCM"){
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/Nadir24950/complete-prodcution-e2e-pipeline'
            }

        }
         stage("Build Application"){
            steps {
                sh "mvn clean package"
            }

        }
         stage("Test Application"){
            steps {
                sh "mvn test"
            }

        }
        stage("SonarQube Analysis"){
            steps{
                withSonarQubeEnv(installationName:"sonarqube-scanner",credentialsId:"jenkins-sonarqube-token"){
                    sh "mvn sonar:sonar "
                }
            }
        }
        stage("Quality Gate"){
            steps {
                script{
                    timeout(time: 1, unit: 'HOURS') {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            error "Pipeline aborted due to quality gate failure: ${qg.status}"
                        }
                    }
                }  
            }
        }
        stage("Build and push Docker Image"){
            steps{
                script {
                    docker.withRegistry('', DOCKER_PASS) {
                        docker_image = docker.build "${IMAGE_NAME}"
                    }

                    docker.withRegistry('', DOCKER_PASS) {
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push("latest")
                    }
                }
                
        }
    }
}