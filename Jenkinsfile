pipeline {
    agent any

    stages {

        stage('Install Dependency') {
            steps {
                sh '''
                php -v

                curl -sS https://getcomposer.org/installer | php
                php composer.phar install --no-interaction --prefer-dist

                if [ ! -f .env ]; then
                    cp .env.example .env
                fi

                php artisan key:generate
                '''
            }
        }

        stage('Deploy to Server') {
            steps {
                sshagent(['ssh-prod']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no root@172.23.1.152 "mkdir -p /root/prod_server"

                    scp -o StrictHostKeyChecking=no -r * root@172.23.1.152:/root/prod_server/
                    '''
                }
            }
        }

    }
}