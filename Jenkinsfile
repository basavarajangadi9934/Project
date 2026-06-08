pipeline {
    agent any
    environment {
        IMAGE = 'springboot-image'
        TAG = "${env.GIT_COMMIT}"
        CLUSTER = 'argocd-cluster'
        DOCKERHUB_REPO = 'basavaraj1234/argocd-springboot'
        REGISTRY = 'docker.io'

    }
    stages {
        stage('checkout scm'){
            steps {
                checkout scm
            }
        }
        stage('unit test'){
            steps {
                sh 'mvn clean test'
            }
        }
        //stage('static analysis'){
          //  steps {
            //    sh 'mvn sonar:sonar'
            //}
        //}
        //stage('quality gate'){
          //  steps {
            //    timeout(time: 5, unit: 'MINUTES')
              //  waitForQualityGate abortPipeline: true
            //}
        //}
        stage('build docker image'){
            steps {
                sh '''
                    docker build -t $IMAGE:$TAG . \
                    docker tag $IMAGE:$TAG $DOCKERHUB_REPO:$TAG  
                    '''
            }
        }
        stage('scan docker image'){
            steps {
                sh '''
                    trivy image $IMAGE:$TAG \
                    --severity HIGH, CRITICAL \
                    --exit-code 1
                    '''
            }
        }
        stage('push image to dockerHub') {
            withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKERUSER', passwordVariable: 'DOCKER_PASSWORD')]){
                sh '''
                    echo $DOCKER_PASSWORD | docker login -u $DOCKERUSER --password-stdin $REGISTRY
                    docker push $DOCKERHUB_REPO:$TAG
                    '''
            }
        }
        stage('update kubernetes manifest'){
            steps{
                sh '''
                    sed -i "s|image: .*|image: $DOCKERHUB_REPO:$TAG|" kubernetes-manifest/Deployment.yaml
                    git config --global user.email "ci@example.com"
                    git config --global user.name "jenkins"
                    git add kubenetes-manifest/Deployment.yaml
                    git commit -m "update image to $DOCKERHUB_REPO:$TAG"
                    git push -u origin main
            }
        }
    }
}
