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
            withKubeCredentials(kubectlCredentials: [[caCertificate: 'LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMvakNDQWVhZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJeU1USXlOVEU1TlRJek5Gb1hEVE15TVRJeU1qRTVOVEl6TkZvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTmFDCmZkeDhRYnFsNUVzUUxXa1hNZzlWdkE3MExGVDRiODZGVFZvWWJBdUZaeExiUy9xTXRyMkhiRTM4Rk9nbjlPREgKbXZWSW0velBISy9GL0Z6K1EzQzFSMXk5U2w2MklYVmdaNG4rRHJhZW1ZdGRsMytXN1VxaDR1dHNNUFpOdlpVdgpVWDM5aE1NcnVNOVpzRndoMXhWMzJxODh0dHpPMFc4R0dlN0ZsOEFFVjlrOWhERU5yZkZ3L3RMZ2ppOGFnU1Q3CjlhbGw5d1dsdzNxUUtzMFhWcms0T1VIUmExTHNFbmFia2sybHFua1ZOaFQ3RUsyWkZDMk5rLzFESFJzTWZRdGMKZ3ZKWTRDaGErTlFwRVRWUkcrSXZzTEs4RUhSOHZJUTJoM2hWb1lLV3VKdm9RaW9EcCtIRThOc1pVTUlMWUdIeQpmTHJGaWhiWHVLV1VIalc4YTRNQ0F3RUFBYU5aTUZjd0RnWURWUjBQQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCCi93UUZNQU1CQWY4d0hRWURWUjBPQkJZRUZIN3JLWGJobU84WHFkM0pKMzRYZnVzWmdTelBNQlVHQTFVZEVRUU8KTUF5Q0NtdDFZbVZ5Ym1WMFpYTXdEUVlKS29aSWh2Y05BUUVMQlFBRGdnRUJBSGZGQktodDBQc2kvRG5EU1lOVQpNMTkxTGpEbms4aUFjcmwzbjlrSWJuT2U0MFF0M3V1NFFZZzBJOVRpa1VwSnpZMkZnREtSNlg3TVBtcElrZ0ZqCmpGMlZrVXlqekNOa01Zb1VVL3I2RURnbXNPbXBIdHlHNXFUVEM1aS9xNEpFUTVLa3F6U0Z3T1I1RUJ4RDZoMzgKRUpHWkhEei8vd21BMlFjWU1KejZUZjNLeTVPcUkrNnJHOXJrelJuUnRHYm9ybk90RzhTN29FQUo4NktDeXB2SAo4cWRNWHQ4NE1MV08xckJoMW9JN0JYWkV0TkNHMzJ5emkwM21nMEd5RGNRWDluMWlLNmRPcnp2dElka1VnOUNYCllkc2tzajlEUE4yTGpJOVZoYXQzSVlORVRFRkhwbS9LelJBOUlvQm02WTlhem1BUVBtamprd2FaRkVpMnQvbmUKQW1VPQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==', clusterName: 'pycalc1', contextName: 'nadav@pycalc1.eu-central-1.eksctl.io', credentialsId: 'Kubectl', namespace: 'default', serverUrl: 'https://C0B96C9DCF7B80ECFD890C1302DB489C.yl4.eu-central-1.eks.amazonaws.com']]) {
            sh 'kubectl apply -f calc-deploy.yml'
            }     
            
            }
        }
    }
}
