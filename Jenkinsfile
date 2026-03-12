pipeline {
    agent any

    stages {

        stage('Install Dependency') {
    steps {
        sh '''
        docker run --rm -v $PWD:/app -w /app php:8.4-cli bash -c "
        apt-get update &&
        apt-get install -y curl zip unzip &&
        curl -sS https://getcomposer.org/installer | php &&
        mv composer.phar /usr/local/bin/composer &&
        composer install --no-interaction --prefer-dist &&
        cp .env.example .env &&
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

                    scp -o StrictHostKeyChecking=no -r . root@172.23.1.152:/root/prod_server/
                    '''
                }
            }
        }

    }
}