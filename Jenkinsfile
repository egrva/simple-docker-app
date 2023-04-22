pipeline {
    agent any
    tools{
        maven 'maven 3.9.1'
    }
    stages{
        stage('Build Maven'){
            steps{
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/egrva/simple-docker-app']]])
                sh 'mvn clean install'
            }
        }
        stage("SonarQube Analysis") {
            steps {
                    script {
                        withCredentials([string(credentialsId: 'sonarkube-password', variable: 'sonarqubepwd')]) {
                        sh 'mvn sonar:sonar -Dsonar.login=admin -Dsonar.password=${sonarqubepwd}'
}
                    }
            }
        }
        stage('Build docker image'){
            steps{
                script{
                    sh 'docker build -t egrva/springboot-k8s-example:1.0 .'
                }
            }
        }
        stage('Push image to Hub'){
            steps{
                script{
                   withCredentials([string(credentialsId: 'dockerhub-pwd', variable: 'dockerhubpwd')]) {
                   sh 'docker login -u egrva -p ${dockerhubpwd}'

}
                   sh 'docker push egrva/springboot-k8s-example:1.0'
                }
            }
        }
        stage('Deploy to k8s'){
            steps{
                script{
                    kubernetesDeploy (configs:'deploymentservice.yaml', kubeconfigId: 'k8sconfig')
                }
            }
        }
    }
}