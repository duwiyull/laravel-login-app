pipeline {
    agent any

    stages {

        stage('Build & Install') {
            steps {
                script {
                    docker.image('php:8.4-cli').inside('-u root') {
                        sh '''
                            apt-get update && apt-get install -y zip unzip curl
                            
                            curl -sS https://getcomposer.org/installer -o composer-setup.php
                            php composer-setup.php --install-dir=/usr/local/bin --filename=composer
                            rm composer-setup.php
                            
                            composer install --no-interaction --prefer-dist
                            
                            if [ ! -f .env ]; then cp .env.example .env; fi
                            php artisan key:generate
                        '''
                    }
                }
            }
        }

        stage('Deploy to Simulation Server') {
            steps {
                echo "Deploy Stage"
            }
        }

    }
}