pipeline {
    agent any
    environment {
        ANSIBLE_SERVER = "34.90.111.62"
    }
    stages {
        stage("Copying files to ansible server") {
            steps {
                script {
                    echo "Copying all necessary files to ansible control node"
                    sshagent(['ansible-server-key']) {
                        sh "scp -o StrictHostKeyChecking=no ansible/* austin@${ANSIBLE_SERVER}:/home/austin"
                        withCredentials([sshUserPrivateKey(credentialsId: 'ec2-server-key', keyFileVariable: 'keyfile', usernameVariable: 'user')]){
                            sh 'scp $keyfile austin@$ANSIBLE_SERVER:/home/austin/.ssh/my-key-pair.pem'
                        }
                    }
                    
                }
            }
        }
        stage("Execute ansible playbook"){
            steps {
                script {
                    echo "Calling ansible playbook to configure ec2 instances"
                    def remote = [:]
                    remote.name = "ansible-server"
                    remote.host = ANSIBLE_SERVER
                    remote.allowAnyHosts = true
                    
                    withCredentials([sshUserPrivateKey(credentialsId: 'ansible-server-key', keyFileVariable: 'keyfile', usernameVariable: 'user')]){
                        remote.user = user
                        remote.identityFile = keyfile
                        
                        sshScript remote: remote, script: "prepare-ansible-server.sh"
                        sshCommand remote: remote, command: "ansible-playbook my-playbook.yaml"
                    }
                    
                }
            }
        }
    }   
}