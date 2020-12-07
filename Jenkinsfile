pipeline {
    agent any
    stages {
        stage('Build') { 
            steps {
                sh 'mvn -B -DskipTests clean package' 
            }
        }
        stage('Copy Latest Version To ROOT') { 
            steps {
                sh '''
                    rm -fr ROOT
                    mkdir -p ROOT
                    cp -r ./target/ContainerHelloWorldPOC-1.0-SNAPSHOT/* ./ROOT/
                '''
            }
        }
        stage('Build Docker Image') { 
            steps {
                sh '''
                    docker build -t container_helloworld_poc:${BUILD_NUMBER} .
                '''
            }
        }
        stage('Create ECR Repo, if doesnt exist') { 
            steps {
                sh '''
                    REPO_NAME="container_helloworld_poc"
                    aws ecr describe-repositories --repository-names ${REPO_NAME} 2> /dev/null || aws ecr create-repository --repository-name ${REPO_NAME}
                '''
            }
        }
        stage('Push Image to ECR Repo') { 
            steps {
                sh '''
                    aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 099252316056.dkr.ecr.us-east-1.amazonaws.com
                    docker tag container_helloworld_poc:${BUILD_NUMBER} 099252316056.dkr.ecr.us-east-1.amazonaws.com/container_helloworld_poc:${BUILD_NUMBER}
                    docker push 099252316056.dkr.ecr.us-east-1.amazonaws.com/container_helloworld_poc:${BUILD_NUMBER}
                '''
            }
        }
        stage ("Call Terraform Build Job") {		
            steps {
                build job: 'terraform-ecs-downstream', parameters: [string(name: 'BUILD_VERSION', value: "${BUILD_NUMBER}")]
            }
        }
    }
}