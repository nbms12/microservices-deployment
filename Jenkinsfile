pipeline {
    agent any 

    stages {
        stage('deploy to kubenetes cluster..') {
            steps {
                withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'EKS-1', contextName: '', credentialsId: 'k8-token', namespace: 'webapps', serverUrl: 'https://69A0F3F7E5BB900B20EEE84988DF70E9.gr7.ap-south-1.eks.amazonaws.com']]) {
                     // some block
                  sh 'kubectl apply -f deployment-service.yml'
                  sleep 60
                 }
            }
        }
        stage('verify deployments..') {
            steps {
                withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'EKS-1', contextName: '', credentialsId: 'k8-token', namespace: 'webapps', serverUrl: 'https://69A0F3F7E5BB900B20EEE84988DF70E9.gr7.ap-south-1.eks.amazonaws.com']]) {
                   // some block
                   sh 'kubectl get svc -n webapps'
              }
            }
        }
    }
}
