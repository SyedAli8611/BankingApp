pipeline {
    agent any

    environment {    
        DOCKERHUB_CREDENTIALS = credentials('dockerid')
    }
        
    stages {
        stage('SCM Checkout') {
            steps {
                echo 'Perform SCM Checkout'
                git 'https://github.com/SyedAli8611/BankingApp.git'
            }
        }
        stage('Application Build') {
            steps {
                echo 'Perform Application Build'
                sh 'mvn clean package'
            }
        }
        stage('Docker Build') {
            steps {
                echo 'Perform Docker Build'
                sh "docker build -t iamfaizanali/bank-automatic:${BUILD_NUMBER} ."
                sh "docker tag iamfaizanali/bank-automatic:${BUILD_NUMBER} iamfaizanali/bank-automatic:latest"
                sh 'docker image list'
            }
        }
        stage('Login to Dockerhub') {
            steps {
                echo 'Login to DockerHub'                
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }
        stage('Publish the Image to Dockerhub') {
            steps {
                echo 'Publish to DockerHub'
                sh "docker push iamfaizanali/bank-automatic:${BUILD_NUMBER}"                
                sh "docker push iamfaizanali/bank-automatic:latest"
            }
        }

        stage('Execute Ansible Playbook') {
            steps {
                script {
                    ansiblePlaybook become: true, credentialsId: 'cred-ansible', disableHostKeyChecking: true, installation: 'ansible', inventory: '/etc/ansible/hosts', playbook: 'ansible-playbook.yml', vaultTmpPath: ''
                }
            }
        }
    }
    
    post {
        failure {
            echo 'Send mail on failure'
            mail bcc: 'alisyedfaizan390@gmail.com', body: 'Banking app build failed', cc: 'alisyedfaizan390@gmail.com', from: '', replyTo: '', subject: 'Banking app build failed', to: 'alisyedfaizan390@gmail.com'
        }
        success {
            echo 'Send mail on success'
            mail bcc: 'alisyedfaizan390@gmail.com', body: 'Banking app build successful ', cc: 'alisyedfaizan390@gmail.com', from: '', replyTo: '', subject: 'Banking app build successful with automation', to: 'alisyedfaizan390@gmail.com'
        }
    }
}
