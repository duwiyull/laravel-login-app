pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Laravel') {
            steps {
                script {
                    docker.image('php:8.4-cli').inside('-u root') {
                        sh '''
                            apt-get update
                            apt-get install -y git zip unzip curl

                            # Install Composer
                            curl -sS https://getcomposer.org/installer -o composer-setup.php
                            php composer-setup.php --install-dir=/usr/local/bin --filename=composer
                            rm composer-setup.php

                            # Install dependency Laravel
                            composer install --no-interaction --prefer-dist

                            # Setup environment Laravel
                            if [ ! -f .env ]; then
                                cp .env.example .env
                            fi

                            php artisan key:generate
                        '''
                    }
                }
            }
        }

        stage('Deploy to WSL Server') {
            steps {
                sshagent(credentials: ['ssh-prod']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no root@172.23.1.152 "mkdir -p /root/prod_server"

                        scp -o StrictHostKeyChecking=no -r ./* root@172.23.1.152:/root/prod_server/

                        ssh -o StrictHostKeyChecking=no root@172.23.1.152 "
                            cd /root/prod_server

                            docker rm -f laravel-online || true

                            docker run -d --name laravel-online \
                                -p 8001:8000 \
                                -v /root/prod_server:/var/www/html \
                                -w /var/www/html \
                                php:8.4-cli php artisan serve --host=0.0.0.0

                            docker exec laravel-online cp .env.example .env || true
                            docker exec laravel-online php artisan key:generate
                            docker exec -u root laravel-online chmod -R 777 storage bootstrap/cache
                        "

                        echo "Laravel running on port 8001"
                    '''
                }
            }
        }

    }
}