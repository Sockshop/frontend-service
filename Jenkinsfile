pipeline {
    environment { //for docker
        DOCKER_ID = credentials('DOCKER_ID')
        DOCKER_IMAGE_FRONT_END = "front-end"
        DOCKER_TAG = "${BUILD_ID}"
        BUILD_AGENT  = ""
        NAMESPACE = "sockshop"
    }
agent any
    stages {
        /*stage('Build') {
            steps { //create a loop somehow??
            sh 'docker build -t $DOCKER_ID/$DOCKER_IMAGE_FRONT_END:$DOCKER_TAG .'
                   }
        }
        stage('Run') {
            steps {
                sh 'docker network create $BUILD_TAG'
                sh 'docker run -d --name $DOCKER_IMAGE_FRONT_END --rm --network $BUILD_TAG $DOCKER_ID/$DOCKER_IMAGE_FRONT_END:$DOCKER_TAG'
                sh 'docker stop $DOCKER_IMAGE_FRONT_END'
                sh 'docker network rm $BUILD_TAG'    
            }
        }
        stage('Push') {
            environment {
                DOCKER_PASS = credentials("DOCKER_HUB_PASS")
            }
            steps {
                sh 'docker image tag $DOCKER_ID/$DOCKER_IMAGE_FRONT_END:$DOCKER_TAG $DOCKER_ID/$DOCKER_IMAGE_FRONT_END:latest'
                sh 'docker login -u $DOCKER_ID -p $DOCKER_PASS'
                sh 'docker push $DOCKER_ID/$DOCKER_IMAGE_FRONT_END:$DOCKER_TAG && docker push $DOCKER_ID/$DOCKER_IMAGE_FRONT_END:latest'
            }
        }*/
        stage('Deploy EKS') {
            environment { // import Jenkin global variables 
                //KUBECONFIG = credentials("EKS_CONFIG")  
                AWS_ACCESS_KEY_ID = credentials('AWS_ACCESS_KEY_ID')
                AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
                AWSREGION = "eu-west-3"
                EKSCLUSTERNAME = "sock-shop-eks"
            }
            steps {
                script {
                    dir('manifests') {
                        sh "aws eks update-kubeconfig --name sock-shop-eks --region $AWSREGION"
                        //sh "kubectl apply -f nginx-deployment.yaml"
                        //sh "kubectl apply -f nginx-service.yaml"
                        //sh 'kubectl get namespace'
                        //sh 'kubectl create namespace $NAMESPACE'
                        sh 'ls'
                        sh 'kubectl apply -f ./deployment.yaml -n $NAMESPACE'
                        sh 'kubectl apply -f ./service.yaml -n $NAMESPACE'
                        sh 'aws configure set output text'                
                        //sh 'aws eks --region $AWSREGION update-kubeconfig --name $EKS_CLUSTER --kubeconfig .kube/config' 
                        //sh 'aws eks list-clusters'
                        /*sh 'kubectl cluster-info --kubeconfig .kube/config'
                        sh 'kubectl apply -f ./manifests -n NAMESPACE --kubeconfig .kube/config'
                        sh 'sleep 30'
                        sh 'kubectl get ingress -n $NAMESPACE'
                        sh 'kubectl get pods -n $NAMESPACE'
                        sh 'kubectl get svc -n $NAMESPACE'*/
                        
                    }
                }
            }
        }
    }           
}
