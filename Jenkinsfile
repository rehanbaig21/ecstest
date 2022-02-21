properties([pipelineTriggers([githubPush()])])
pipeline {
  agent any
  environment {
    DOCKER_REGISTRY = '927956577355.dkr.ecr.us-east-1.amazonaws.com'
    DOCKER_REPOSITORY = "ecs-test"
    aws_credentials= credentials('ecstest')
    GIT_COMMIT_SHORT = sh(
                script: "printf \$(git rev-parse --short ${GIT_COMMIT})",
                returnStdout: true )
  

stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/docker']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'jenkins-github-user', url: 'https://github.com/rehanbaig21/ecstest']]])
                sh "ls -lart ./*"
}        
        }
       
       stage('docker login') {
       environment {
        GET_PASSWORD = 'aws ecr get-login-password --region us-east-1'
        LOGIN = "docker login --username AWS --password-stdin $DOCKER_REGISTRY"
      }
      steps {
        sh(script: "$GET_PASSWORD | $LOGIN", returnStdout:true)
      }
    }

     stage('docker build ') {
       
      steps {
        sh "docker build ."
        sh "docker images"
        sh " docker tag hello-world $DOCKER_REGISTRY/$DOCKER_REPOSITORY/ecstest-$GIT_COMMIT_SHORT"
        sh "echo $GIT_COMMIT_SHORT"
       }
}
}
}