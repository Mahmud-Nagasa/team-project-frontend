pipeline {
    agent any
    environment {
        AWS_REGION = 'eu-west-2'
        ECR_REGISTRY = '494108812211.dkr.ecr.eu-west-2.amazonaws.com'
        ECR_REPOSITORY = 'lovegames-project-frontend'
        HELM_CHART = 'project-frontend'
        INITIAL_VERSION = 1
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
                sh "aws ecr get-login-password --region ${AWS_REGION} --no-include-email | sh -"
                sh "docker build -t ${ECR_REPOSITORY}:latest ."
            }
        }
        stage('push') {
            steps {
                script {
                    docker.withRegistry("https://${ECR_REGISTRY}", "ecr:${AWS_REGION}:admin") {
                        docker.image("${ECR_REPOSITORY}").push("${VERSION}")
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                echo "deploying"
                sh 'kubectl get nodes'
                sh "helm install ${HELM_CHART} ./${HELM_CHART}"
            }
        }
    }
}
