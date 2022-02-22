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
  }

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

     stage('docker build and push') {
       
      steps {
        sh "docker build ."
        sh " docker tag hello-world $DOCKER_REGISTRY/$DOCKER_REPOSITORY:$GIT_COMMIT_SHORT"
        sh "echo $GIT_COMMIT_SHORT"
        sh "docker push $DOCKER_REGISTRY/$DOCKER_REPOSITORY:$GIT_COMMIT_SHORT"
       }
}
    stage('update task defination config') {
 steps {
    sh 'sed -i "s|updatedimage|$DOCKER_REGISTRY/$DOCKER_REPOSITORY:$GIT_COMMIT_SHORT|g" ./ecs_task_defination.json'
 }
    }
   
   stage('update task defination config with new image') {
   environment {
        NEW_IMAGE='"$DOCKER_REGISTRY/$DOCKER_REPOSITORY:$GIT_COMMIT_SHORT"'
        TASK_DEFINITION='aws ecs describe-task-definition --task-definition "hello_world" --region "us-east-1"'
        NEW_TASK_DEFINTIION="echo $TASK_DEFINITION | jq --arg IMAGE $NEW_IMAGE '.taskDefinition | .containerDefinitions[0].image = $IMAGE | del(.taskDefinitionArn) | del(.revision) | del(.status) | del(.requiresAttributes) | del(.compatibilities)'"
      }    
 steps {
     sh 'echo $NEW_IMAGE'
     sh 'aws ecs register-task-definition --region "us-east-1" --cli-input-json "$NEW_TASK_DEFINTIION"'
 }
}
}
}
