pipeline {
    agent any
    environment {
        ENV_NAME = "${env.repo_branch == "dev" ? "dev" : "prod"}"
    }
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
                sh 'packer build -var env=${ENV_NAME} -var app_repo=${app_repo} -var app_name=${app_name} -var ec2_ip=${ec2_ip} -var app_port=${app_port} -var repo_branch=${repo_branch} buildAMI.json'   
            }
        }
        stage ('Deploy APP') {
            steps {
                build job: 'DEPLOY', parameters: [
                    string(name: 'deploy_env', value: "${env.ENV_NAME}"),
                    string(name: 'project_name', value: "${env.app_name}")
                ]
            }
        }
    }
}