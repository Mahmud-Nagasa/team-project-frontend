
pipeline {
    agent {
        docker {
            image 'node:14' 
            args '-p 3000:3000' 
        }
    }
    environment {
        AWS_REGION = 'eu-west-2'
        ECR_REGISTRY = '494108812211.dkr.ecr.eu-west-2.amazonaws.com'
        ECR_REPOSITORY = 'lovegamesfrontend'
        INITIAL_VERSION = '1'
        HELM_CHART = 'project-frontend'
        VERSION = "${INITIAL_VERSION}.${env.BUILD_NUMBER}"
    }
    triggers {
        pollSCM('* * * * *')
    }
    stages {
        stage('Build') {
            steps {
                echo "Running ${env.BUILD_NUMBER} in ${AWS_REGION}"
                echo "building + pushing to container repository"
                sh 'npm install'
                sh "aws ecr get-login-password --region ${AWS_REGION} --no-include-email | sh -"
                sh "docker build -t ${ECR_REGISTRY}/${ECR_REPOSITORY}:${VERSION} ."
                sh "docker push ${ECR_REGISTRY}/${ECR_REPOSITORY}:${VERSION}"
            }
        }
    }
}

