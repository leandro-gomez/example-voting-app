pipeline {
    agent none

    stages {
        stage('worker build') {
            agent {
                docker {
                    image 'maven:3.6.1-jdk-8-alpine'
                    args '-v $HOME/.m2:/root/.m2'
                }
            }
            when {
                changeset '**/worker/**'
            }
            steps {
                echo 'Compiling worker app'
                dir('worker') {
                    sh 'mvn compile'
                }
            }
        }
        stage('worker test') {
            agent {
                docker {
                    image 'maven:3.6.1-jdk-8-alpine'
                    args '-v $HOME/.m2:/root/.m2'
                }
            }

            when {
                changeset '**/worker/**'
            }
            steps {
                echo 'Running tests'
                dir('worker') {
                    sh 'mvn clean test'
                }
            }
        }
        stage('docker-package') {
            agent any // Work in jenkins host
            when {
                changeset '**/worker/**'
                branch 'master'
            }
            steps {
                echo 'Packaging worker app with Docker'
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
                         def workerImage = docker.build("lgomezdevartis/worker:${env.BUILD_ID}", "./worker")
                         workerImage.push()
                         workerImage.push("${env.BRANCH_NAME}")
                    }
                }
            }
        }
        stage('result build') {
            agent {
                docker {
                    image 'node:8.16.0-alpine'
                }
            }
            when {
                changeset '**/result/**'
            }
            steps {
                echo 'Compiling result app'
                dir('result') {
                    sh 'npm install'
                }
            }
        }
        stage('result test') {
            agent {
                docker {
                    image 'node:8.16.0-alpine'
                }
            }
            when {
                changeset '**/result/**'
            }
            steps {
                echo 'Running tests'
                dir('result') {
                    sh 'npm install'
                    sh 'npm test'
                }
            }
        }
        stage('result docker-package') {
            agent any // Work in jenkins host
            when {
                changeset '**/result/**'
                branch 'master'
            }
            steps {
                echo 'Packaging result app with Docker'
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
                         def workerImage = docker.build("lgomezdevartis/result:${env.BUILD_ID}", "./result")
                         workerImage.push()
                         workerImage.push("${env.BRANCH_NAME}")
                    }
                }
            }
        }
        stage('vote build') {
            agent {
                docker {
                    image 'python:3.6.12-alpine'
                    args '--user 0:0'
                }
            }
            when {
                changeset '**/vote/**'
            }
            steps {
                echo 'Compiling vote app'
                dir('vote') {
                    sh 'pip install --no-cache -r requirements.txt'
                }
            }
        }
        stage('vote test') {
            agent {
                docker {
                    image 'python:3.6.12-alpine'
                    args '--user 0:0'
                }
            }
            when {
                changeset '**/vote/**'
            }
            steps {
                echo 'Running tests'
                dir('vote') {
                    sh 'pip install --no-cache -r requirements.txt'
                    sh 'nosetests -v'
                }
            }
        }
        stage('vote docker-package') {
            agent any // Work in jenkins host
            when {
                changeset '**/vote/**'
                branch 'master'
            }
            steps {
                echo 'Packaging vote app with Docker'
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
                         def workerImage = docker.build("lgomezdevartis/vote:${env.BUILD_ID}", "./vote")
                         workerImage.push()
                         workerImage.push("${env.BRANCH_NAME}")
                    }
                }
            }
        }


    }
    
    post {
        always{
            echo "Finished!"
        }
    }
}

