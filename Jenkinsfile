pipeline {
  agent { label 'ecs' }

  environment {
    PROJECT = "push-s3-alb-access-logs-to-elasticsearch"
    BUILD_ZIP_NAME = "${PROJECT}-build-${BUILD_NUMBER}.zip"
    AWS_DEFAULT_REGION = "eu-west-1"
  }

  stages {
     stage('Credentials/Meta') {
       steps {
         sh '$(aws ecr get-login --no-include-email --region eu-west-1)'
       }
     }

     stage('Build') {
       steps {
         sh 'docker run --rm -v ${WORKSPACE}:/var/task lambci/lambda:build-nodejs8.10 npm install --production'
         sh 'docker run -v ${WORKSPACE}:/app -w /app kramos/alpine-zip -r $BUILD_ZIP_NAME src node_modules .env'
       }
     }

     stage('Publish') {
       steps {
           sh 'aws lambda update-function-code --function-name $PROJECT --zip-file fileb://$BUILD_ZIP_NAME --publish --query \'Version\' --output text > lambdaVersion'
       }
     }
   }
}
