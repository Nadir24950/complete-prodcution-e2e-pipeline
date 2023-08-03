pipeline{
    agent{
        label "jenkins-agent"
    }
    tools {
        jdk 'Java20'
        maven 'Maven3'
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
                imeout(time: 1, unit: 'HOURS') {
                    def qg = waitForQualityGate()
                    if (qg.status != 'OK') {
                        error "Pipeline aborted due to quality gate failure: ${qg.status}"
                    }
                }
            }
        }
    }
}