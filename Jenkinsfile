pipeline {
    agent {
        label 'agent3'
    }
  stages {
    stage('Clone app repository') {
      steps {
        // use the git step to clone the repository
        git credentialsId: 'GH-nad', branch: 'main', url: 'https://github.com/N4dav/PyCalc.git'
      }
    }
    stage('Build Docker Image') {
      steps {
               sh '''
               GIT_COMMIT=$(git rev-parse HEAD)
               docker build -t 808633698297.dkr.ecr.eu-central-1.amazonaws.com/pycalc:$GIT_COMMIT-$BUILD_NUMBER /home/ec2-user/jenkins/workspace/PyCalc/flask-calculator-main/.
               '''
        }
    }
    stage('Push Image to ECR') {
        steps {
            sh '''
            GIT_COMMIT=$(git rev-parse HEAD)
            docker push 808633698297.dkr.ecr.eu-central-1.amazonaws.com/pycalc:$GIT_COMMIT-$BUILD_NUMBER
            '''
            }
    }
    stage('Connect to remote EKS cluster') {
        steps {
            // aws eks update-kubeconfig --name CLUSTERNAME --region REGIONCODE
            sh '''
            aws eks update-kubeconfig --name pycalc --region eu-central-1
            '''
            }
    }    
    stage('Deploy to EKS') {
        steps {
            sh '''
            kubectl apply -f /home/ec2-user/jenkins/workspace/PyCalc/flask-calculator-main/calc-deploy.yml
            export ELB_NAME=\$(kubectl get svc pycalc -o jsonpath='{.status.loadBalancer.ingress[0].hostname}')
            kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.44.0/deploy/static/provider/cloud/deploy.yaml
            kubectl apply -f /home/ec2-user/jenkins/workspace/PyCalc/flask-calculator-main/ingress.yml
            kubectl get ingress pycalc
            '''
            }
        }
    }
}
