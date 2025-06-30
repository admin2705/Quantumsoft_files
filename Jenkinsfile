pipeline {
    agent { label 'agent_007' }

    environment {
        DOCKERHUB_CREDENTIALS = 'docker_creds'
        REPO_URL = 'https://github.com/admin2705/Quantumsoft_files.git'
        GITHUB_CREDENTIALS = 'github_creds'
        BACKEND_IMAGE = 'pmasne98/backend:latest'
        FRONTEND_IMAGE = 'pmasne98/frontend:latest'
        KUBERNETES_FILES = 'k8s_creds'
    }

    stages {
        stage('Clone') {
            steps {
                git branch: 'main', credentialsId: "${GITHUB_CREDENTIALS}", url: "${REPO_URL}"
            
            }
        }

        stage('Build & Push Backend Image') {
            steps {
                dir('backend') {                
                  withDockerRegistry(credentialsId: "${DOCKERHUB_CREDENTIALS}", url: '') {
                    sh "docker build --no-cache -t ${BACKEND_IMAGE} ."
                    sh "docker push ${BACKEND_IMAGE}"
                    }
                }
            }
        }

        stage ('Build & Push Frontend Image') {
            steps {
                dir('frontend') {
                    withDockerRegistry(credentialsId: "${DOCKERHUB_CREDENTIALS}", url: '') {
                        sh "docker build --no-cache -t ${FRONTEND_IMAGE} ."
                        sh "docker push ${FRONTEND_IMAGE}"
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                dir('k8s_manifest'){
                withCredentials([file(credentialsId: "${KUBERNETES_FILES}", variable: 'KUBECONFIG')]){
                sh '''
                 kubectl delete all --all
                 kubectl apply -f backend-deployment.yml
                 kubectl apply -f frontend-deployment.yml
                 kubectl apply -f mysql-deployment.yml
                 sleep 20
                 kubectl get all

                 echo "second update"

                 sleep 20
                 kubectl get all                 

                '''
                
                }
                }
            }
        }
    }
}
