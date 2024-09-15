pipeline {
    agent any

    stages {
        stage('Deploy Kubernetes') {
            steps {
                withKubeCredentials(kubectlCredentials: [[
                    caCertificate: '',
                    clusterName: 'EKS-1',
                    contextName: '',
                    credentialsId: 'k8-token',
                    namespace: 'webaaps',
                    serverUrl: 'https://FACF5859BB7FEC1D667A82242A1A14E0.gr7.ap-south-1.eks.amazonaws.com'
                ]]) {
                    sh "kubectl apply -f deployment-service.yml"
                    sleep 60
                }
            }
        }
        
        stage('Verify Deployment') {
            steps {
                withKubeCredentials(kubectlCredentials: [[
                    caCertificate: '',
                    clusterName: 'EKS-1',
                    contextName: '',
                    credentialsId: 'k8-token',
                    namespace: 'webaaps',
                    serverUrl: 'https://FACF5859BB7FEC1D667A82242A1A14E0.gr7.ap-south-1.eks.amazonaws.com'
                ]]) {
                    sh "kubectl get svc -n webaaps"
                }
            }
        }
    }
}

