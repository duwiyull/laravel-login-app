pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                git 'https://github.com/duwiyull/laravel-login-app.git'
            }
        }

        stage('Install Dependency') {
            steps {
                sh '''
                docker run --rm \
                -v $PWD:/app \
                -w /app \
                php:8.4-cli bash -c "

                apt-get update
                apt-get install -y curl zip unzip

                curl -sS https://getcomposer.org/installer | php
                mv composer.phar /usr/local/bin/composer

                composer install --no-interaction --prefer-dist

                if [ ! -f .env ]; then cp .env.example .env; fi

                php artisan key:generate
                "
                '''
            }
        }

        stage('Deploy to Server') {
            steps {
                sshagent(['ssh-prod']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no root@172.23.1.152 "mkdir -p /root/prod_server"

                    scp -o StrictHostKeyChecking=no -r * root@172.23.1.152:/root/prod_server/

                    ssh -o StrictHostKeyChecking=no root@172.23.1.152 "

                        cd /root/prod_server

                        docker rm -f laravel-app || true

                        docker run -d --name laravel-app \
                        -p 8001:8000 \
                        -v /root/prod_server:/var/www/html \
                        -w /var/www/html \
                        php:8.4-cli php artisan serve --host=0.0.0.0

                    "
                    '''
                }
            }
        }

    }
}