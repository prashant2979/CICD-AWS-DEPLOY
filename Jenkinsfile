
node {
    def appDir = '/var/www/nextjs-app'

    stage('Clean Workspace') {
        echo 'Cleaning Jenkins Workspace'
        deleteDir()
    }

    stage('Clone Repo') {
        echo 'Cloning repository'

        git(
            branch: 'main',
            url: 'https://github.com/prashant2979/CICD-AWS-DEPLOY.git'
        )
    }

    stage('Deploy to EC2') {
        echo 'Deploying Next.js app'

        sh """
            set -e

            sudo mkdir -p ${appDir}
            sudo chown -R jenkins:jenkins ${appDir}

            rsync -av --delete \
                --exclude='.git' \
                --exclude='node_modules' \
                --exclude='.next' \
                --exclude='app.log' \
                ./ ${appDir}/

            cd ${appDir}

            node --version
            npm --version

            npm ci
            npm run build

            if ! command -v pm2 >/dev/null 2>&1; then
                sudo npm install -g pm2
            fi

            pm2 delete nextjs-app || true
            pm2 start npm --name nextjs-app -- run start -- --hostname 0.0.0.0
            pm2 save

            sleep 5
            curl -f http://localhost:3000
        """
    }
}
