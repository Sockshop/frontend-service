pipeline {
    environment { //for docker
        DOCKER_ID = credentials('DOCKER_ID')
        DOCKER_IMAGE_FRONT_END = "front-end"
        DOCKER_TAG = "${BUILD_ID}"
        BUILD_AGENT  = ""
        //NAMESPACE = credentials("NAMESPACE")
    }
agent any
    stages {
        stage('Build') {
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
        }
        stage('Deploy EKS') {
            environment { // import Jenkin global variables 
                //KUBECONFIG = credentials("EKS_CONFIG")  
                AWS_ACCESS_KEY_ID = credentials('AWS_ACCESS_KEY_ID')
                AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
                AWSREGION = credentials("AWS_REGION")
                EKSCLUSTERNAME = credentials("EKS_CLUSTER")
            }
            steps {
                /*sh 'rm -Rf .kube'
                sh 'mkdir .kube'
                sh 'touch .kube/config'
                sh 'sudo chmod 777 .kube/config'
                sh 'rm -Rf .aws'
                sh 'mkdir .aws'*/
                sh 'aws configure set aws_access_key_id $AWS_ACCESS_KEY_ID'
                sh 'aws configure set aws_secret_access_key $AWS_SECRET_ACCESS_KEY'
                sh 'aws configure set region $AWSREGION'
                sh 'aws configure set output text'                
                sh 'aws eks --region $AWSREGION update-kubeconfig --name $EKS_CLUSTER --kubeconfig .kube/config' 
                sh 'aws eks list-clusters'
                sh 'kubectl cluster-info --kubeconfig .kube/config'
                sh 'kubectl apply -f ./manifests -n $NAMESPACE --kubeconfig .kube/config'
            }
        }      
    } 
}
