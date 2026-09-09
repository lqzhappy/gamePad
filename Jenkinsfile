pipeline {

    agent none

    stages {

        stage('Build Web') {

            agent {
                docker {
                    image 'node:22'
                    label 'docker-agent'
                    // args '-v /ext/jenkins-agent/npm-cache:/home/node/.npm'
                }
            }

            steps {

                echo '===== Node 环境 ======'

                sh '''
                    node -v
                    npm -v
                '''

                echo '===== 修改npm代理 ====='
                sh '''
                    npm config set registry http://nexus.lan/repository/npm-group/
                    sed -i 's#https://registry.npmmirror.com/#http://nexus.lan/repository/npm-group/#g' package-lock.json
                    sed -i 's#https://registry.npmjs.org/#http://nexus.lan/repository/npm-group/#g' package-lock.json
                '''
                echo '===== 安装依赖 ====='

                sh '''
                    npm ci
                '''


                echo '===== Webpack Build ====='

                sh '''
                    npm run build
                '''

            }

        }


        stage('Check Build') {

            agent {
                label 'docker-agent'
            }

            steps {

                sh '''
                    echo "===== Build Result ====="

                    ls -lah

                    ls -lah dist

                    cp -r dist/* ../public
                '''
            }

        }

    }

}
