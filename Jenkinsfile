
node {
    def appDir = '/var/www/nextjs-app'

    stage('Clean Workspace') {
        echo 'Cleaning Jenkins Workspace'
        deleteDir()
    }

    stage('Clone Repo') {
        echo 'Cloning the correct repository'

        git(
            branch: 'main',
            url: 'https://github.com/prashant2979/CICD-AWS-DEPLOY.git'
        )
    }

    stage('Deploy to EC2') {
        echo 'Deploying Next.js app to EC2'

        sh """
            set -e

            sudo mkdir -p ${appDir}
            sudo chown -R jenkins:jenkins ${appDir}

            rsync -av --delete \
                --exclude='.git' \
                --exclude='node_modules' \
                ./ ${appDir}/

            cd ${appDir}

            node --version
            npm --version

            npm ci
            npm run build

            sudo fuser -k 3000/tcp || true

            nohup npm run start -- --hostname 0.0.0.0 \
                > ${appDir}/app.log 2>&1 < /dev/null &

            sleep 5
            curl -f http://localhost:3000
        """
    }
}
