env.DOCKER_REGISTRY = 'vanillavladimir'
env.DOCKER_IMAGE_NAME = 'backend-prod'
node('master') {
    stage('Git Pull from Github') {
        sh "git clone https://github.com/geraldGhibran/BPCILSY_BACKEND.git"
    }
    stage('Build Docker Image') {
        sh "cd /var/lib/jenkins/BPCILSY/mern-todo-app/backend/ && docker build --build-arg APP_NAME=backend-prod -t $DOCKER_REGISTRY/$DOCKER_IMAGE_NAME:${BUILD_NUMBER} ."   
    }
    stage('Push Docker Image to Dockerhub') {
        sh "docker push $DOCKER_REGISTRY/$DOCKER_IMAGE_NAME:${BUILD_NUMBER}"
    }
    stage('DeployTo Kubernetes Cluster') {
        sh "kubectl create namespace backend-prod"
        sh "kubectl replace -f /var/lib/jenkins/BPCILSY/mern-todo-app/backend-ssl-prod.yml --force -n backend-prod"
        sh'''cd /var/lib/jenkins/BPCILSY/mern-todo-app/ && sed -i "15d" backend-deployment-prod.yml'''
        sh'''cd /var/lib/jenkins/BPCILSY/mern-todo-app/ && sed -i "14 a \'\\'          image: vanillavladimir/$DOCKER_IMAGE_NAME:${BUILD_NUMBER}" backend-deployment-prod.yml && sed -i "s/''//" backend-deployment-prod.yml'''
        sh "cd /var/lib/jenkins/BPCILSY/mern-todo-app/ && kubectl replace -f backend-deployment-prod.yml -n backend-prod --force"
        sh "kubectl replace -f /var/lib/jenkins/BPCILSY/mern-todo-app/backend-service-prod.yml --force -n backend-prod"
        sh "kubectl replace -f /var/lib/jenkins/BPCILSY/mern-todo-app/backend-ingress-prod.yml --force -n backend-prod"
   }
    stage('Remove Docker Image') {
        sh "docker rmi $DOCKER_REGISTRY/$DOCKER_IMAGE_NAME:${BUILD_NUMBER}"   
    }
    stage('Clean Workspace') {
      cleanWs()
    }
}
