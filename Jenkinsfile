pipeline {
    agent any

    stages {
        stage('Récupération de la configuration Packer + Ansible') {
            steps {
                checkout(
                    [
                        $class: 'GitSCM', 
                        branches: [[name: '*/master']], 
                        doGenerateSubmoduleConfigurations: false, 
                        extensions: [[$class: 'CleanBeforeCheckout']], 
                        submoduleCfg: [], 
                        userRemoteConfigs: [
                            [
                                credentialsId: 'git', 
                                url: 'https://github.com/iambluedev1/jenkins_ansible_packer_ami'
                            ]
                        ]
                    ]
                )
            }
        }
        stage('Create Packer AMI') {
            steps { 
                withEnv(['repo_branch=dev']) {
                    sh "echo 'running in dev mode'" 
                    sh 'packer build -var env=dev -var app_repo=${app_repo} -var app_name=${app_name} -var ec2_ip=${ec2_ip} -var app_port=${app_port} -var repo_branch=${repo_branch} buildAMI.json'
                }
                withEnv(['repo_branch=main']) {
                    sh "echo 'running in prod mode'" 
                    sh 'packer build -var env=prod -var app_repo=${app_repo} -var app_name=${app_name} -var ec2_ip=${ec2_ip} -var app_port=${app_port} -var repo_branch=${repo_branch} buildAMI.json'
                }
            }
        }
    }
}