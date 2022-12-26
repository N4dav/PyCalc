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
            //$(aws ecr get-login-password)
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
            // Pull the image from ECR
            sh 'docker pull 808633698297.dkr.ecr.eu-central-1.amazonaws.com/pycalc:build-$BUILD_NUMBER'
            // Add kubectl to the PATH
            sh 'export PATH=$PATH:~/bin'
            withKubeConfig(clusterName: 'pycalc1', contextName: 'nadav@pycalc1.eu-central-1.eksctl.io', credentialsId: 'Kubectl', namespace: 'default', serverUrl: 'https://C0B96C9DCF7B80ECFD890C1302DB489C.yl4.eu-central-1.eks.amazonaws.com') {
            sh 'kubectl apply -f calc-deploy.yml'
}     
            
            }
        }
    }
}
