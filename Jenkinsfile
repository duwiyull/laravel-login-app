pipeline {
    agent any

    stages {

        stage('Deploy to Server') {
            steps {
                sshagent(['ssh-prod']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no root@172.23.1.152 "mkdir -p /root/prod_server"

                    scp -o StrictHostKeyChecking=no -r * root@172.23.1.152:/root/prod_server/

                    ssh -o StrictHostKeyChecking=no root@172.23.1.152 "
                        cd /root/prod_server
                        php artisan key:generate || true
                    "
                    '''
                }
            }
        }

    }
}