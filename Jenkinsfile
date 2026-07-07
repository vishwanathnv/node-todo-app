pipeline {

    agent any

    environment {

        IMAGE_NAME = "vishu66/nodejs_web"

        IMAGE_TAG = "${BUILD_NUMBER}"

    }


    stages {


        stage('Checkout Code') {

            steps {

                git(
                    url: 'https://github.com/vishwanathnv/node-todo-app.git',
                    branch: 'main'
                )

            }

        }


        stage('Install Dependencies') {

            steps {

                sh '''
                node -v
                npm -v
                npm install
                '''

            }

        }


        stage('Build Docker Image') {

            steps {

                sh '''
                docker build \
                -t ${IMAGE_NAME}:${IMAGE_TAG} .
                '''

            }

        }


        stage('Push Docker Image to Docker Hub') {

            steps {

                withDockerRegistry(
                    credentialsId: 'dockerhub-creds'
                ) {

                    sh '''
                    docker push ${IMAGE_NAME}:${IMAGE_TAG}
                    '''

                }

            }

        }


        stage('Deploy to Kubernetes') {

            steps {

                withKubeConfig(
                    credentialsId: 'kubeconfig'
                ) {

                    sh '''

                    echo "Updating Kubernetes Deployment Image"


                    sed -i "s|vishu66/nodejs_web:v1|${IMAGE_NAME}:${IMAGE_TAG}|g" k8s/deployment.yaml


                    echo "Applying Kubernetes manifests"


                    kubectl apply -f k8s/


                    echo "Checking rollout"


                    kubectl rollout status deployment/todo-app

                    '''

                }

            }

        }


    }


    post {

        success {

            echo "Deployment completed successfully"

        }


        failure {

            echo "Pipeline failed"

        }

    }

}
