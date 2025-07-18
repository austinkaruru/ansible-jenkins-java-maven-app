pipeline {
    agent any
    stages {
        stage("Copying files to ansible server") {
            steps {
                script {
                    echo "Copying all necessary files to ansible control node"
                    sshagent(['ansible-server-key']) {
                        sh "scp -o StrictHostKeyChecking=no ansible/* austin@34.90.111.62:/home/austin"
                        withCredentials([sshUserPrivateKey(credentialsId: 'ec2-server-key', keyFileVariable: 'keyfile', usernameVariable: 'user')]){
                            sh 'scp $keyfile austin@34.90.111.62:/home/austin/my-key-pair.pem'
                        }
                    }
                    
                }
            }
        }
    }   
}