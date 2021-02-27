pipeline{
    agent{ label 'master' }
    stages{
        stage('git chekcout'){
            steps{
                script{
                    git credentialsId: 'git', url: 'https://github.com/Anand-sri/sample-java.git'
                }
            }
        }
        
        stage('sonarqube'){
            steps{
                script{
                    withSonarQubeEnv('sonar') {
                        def sonarscanner = tool name: 'sonar-scanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation' 
                        sh """${sonarscanner}/bin/sonar-scanner -Dsonar.language=java -Dsonar.projectKey=myapp -Dsonar.projectVersion=1.0.0 -Dsonar.projectName=myapp -Dsonar.sources=src -Dsonar.java.binaries=. """
                    }    
                }
            }
        }
        
        stage("Quality Gate") {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        
        stage('Maven build'){
            steps{
                script{
                    withMaven(jdk: 'jdk-8', maven: 'maven') {
                        sh """mvn clean install """
                    }
                }
            }
        }
        
        stage('Ansible deploy'){
            steps{
                script{
                    sh """ansible-playbook tomcat-deploy.yml -vv """
                }
            }
        }  
    }
}
