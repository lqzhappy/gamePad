pipeline {

    agent {
        docker {
            image 'node:22'
            label 'docker-agent'
            // args '-v /ext/jenkins-agent/npm-cache:/home/node/.npm'
        }
    }

    stages {

        stage('Build Web') {

            steps {

                echo '===== Node 环境 ======'

                sh '''
                    node -v
                    npm -v
                '''

                echo '===== 修改npm代理 ======'
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

            steps {

                sh '''
                    echo "===== Build Result ====="

                    ls -lah

                    ls -lah dist

                    cp -r dist/* ../public
                '''

                echo '===== 保存 Jenkins 构建产物 ====='

                archiveArtifacts artifacts: 'dist/**', fingerprint: true
            }

        }
    }

    post {
        success {

            script{

                def duration = currentBuild.durationString
                withCredentials([string( credentialsId: 'bark-key', variable: 'BARK_KEY' )]){
                    sh """
                        curl -sS -G --data-urlencode "title=✅ Jenkins 构建成功" --data-urlencode "body=项目: ${JOB_NAME} 构建: #${BUILD_NUMBER} 状态: SUCCESS 耗时: ${duration} ${BUILD_URL}" "http://bark.lan/\${BARK_KEY}" 
                    """
                }
        } 
        failure {
            script{

                def duration = currentBuild.durationString

                withCredentials([string( credentialsId: 'bark-key', variable: 'BARK_KEY' )]){
                    sh """
                        curl -sS -G --data-urlencode "title=❌ Jenkins 构建失败" --data-urlencode "body=项目: ${JOB_NAME} 构建: #${BUILD_NUMBER} 状态: FAILURE 耗时: ${duration} ${BUILD_URL}" "http://bark.lan/\${BARK_KEY}"
                    """
                }
            }
        } 
    }
}


