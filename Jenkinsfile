pipeline {
    agent any
    environment {
        AWS_ACCESS_KEY_ID     = credentials('ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
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
              
                echo "building + pushing to container repository"
                sh "aws ecr get-login-password --region ${AWS_REGION} --no-include-email | sh -"
                sh "docker build -t ${ECR_REPOSITORY}:latest ."
                writeFile file: 'version.env', text: "VERSION=${VERSION}"
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
                sh "helm upgrade --install ${HELM_CHART} ./${HELM_CHART} --set image.tag=${VERSION}"
            }
        }
    }
}
