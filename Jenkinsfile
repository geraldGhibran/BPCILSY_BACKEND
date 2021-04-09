env.DOCKER_REGISTRY = 'vanillavladimir'
env.DOCKER_IMAGE_NAME = 'backend-staging'
node('master') {
    stage('Git Pull from Github') {
        sh "git clone https://github.com/geraldGhibran/BPCILSY_BACKEND.git"
    }
    stage('Build Docker Image') {
        sh "cd /var/lib/jenkins/BPCILSY/mern-todo-app/backend/ && docker build --build-arg APP_NAME=backend-staging -t $DOCKER_REGISTRY/$DOCKER_IMAGE_NAME:${BUILD_NUMBER} ."   
    }
    stage('Push Docker Image to Dockerhub') {
        sh "docker push $DOCKER_REGISTRY/$DOCKER_IMAGE_NAME:${BUILD_NUMBER}"
    }
    stage('DeployTo Kubernetes Cluster') {
        sh'''cd /var/lib/jenkins/BPCILSY/mern-todo-app/ && sed -i "15d" backend-deployment-staging.yml'''
        sh'''cd /var/lib/jenkins/BPCILSY/mern-todo-app/ && sed -i "14 a \'\\'          image: vanillavladimir/$DOCKER_IMAGE_NAME:${BUILD_NUMBER}" backend-deployment-staging.yml && sed -i "s/''//" backend-deployment-staging.yml'''
        sh "cd /var/lib/jenkins/BPCILSY/mern-todo-app/ && kubectl replace -f backend-deployment-staging.yml -n backend-staging --force"
   }
    stage('Remove Docker Image') {
        sh "docker rmi $DOCKER_REGISTRY/$DOCKER_IMAGE_NAME:${BUILD_NUMBER}"   
    }
    stage('Clean Workspace') {
      cleanWs()
    }
}
