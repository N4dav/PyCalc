pipeline {
    agent {
        label 'agent1'
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
               sh "docker build -t 808633698297.dkr.ecr.eu-central-1.amazonaws.com/pycalc:build-${env.BUILD_NUMBER} ."
        }
    }
    stage('Login to AWS account') {
        steps {
            script {
                def login_password = sh(returnStdout: true, stdout: '/dev/null', script: 'aws ecr get-login-password').trim()
                sh "echo ${login_password} | docker login --username AWS --password-stdin https://808633698297.dkr.ecr.eu-central-1.amazonaws.com"
            }
        }
    }
    stage('Push Image to ECR') {
        steps {
            sh "docker push 808633698297.dkr.ecr.eu-central-1.amazonaws.com/pycalc:build-${env.BUILD_NUMBER}"
        }
    }
        
    stage('Deploy to EKS') {
        agent {
            label 'agent3'
        }
        steps {
            script {
                def login_password = sh(returnStdout: true, stdout: '/dev/null', script: 'aws ecr get-login-password').trim()
                sh "echo ${login_password} | docker login --username AWS --password-stdin https://808633698297.dkr.ecr.eu-central-1.amazonaws.com"
            }
            sh 'kubectl apply -f /home/ec2-user/calc-deploy.yml'
            }
        }
    }
}
