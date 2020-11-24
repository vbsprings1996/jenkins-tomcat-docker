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
    }
}