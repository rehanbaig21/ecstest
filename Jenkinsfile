pipeline {
  agent any
  environment {
    DOCKER_REGISTRY = '927956577355.dkr.ecr.us-east-1.amazonaws.com'
    DOCKER_REPOSITORY = "ecs-test"
    aws_credentials= credentials('ecstest')
  }

stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/docker']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'jenkins-github-user', url: 'https://github.com/rehanbaig21/ecstest']]])
}
        }
}
}