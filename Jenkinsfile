pipeline {
agent any


environment {
    AUTH_IMAGE    = 'new-auth'
    ORDER_IMAGE   = 'new-order'
    PRODUCT_IMAGE = 'new-product'
}

stages {

    stage('Clone Repository') {
        steps {
            echo '📥 Cloning GitHub repository...'
            checkout scm
        }
    }

    stage('Build Docker Images') {
        steps {
            echo '🔨 Building Docker images...'

            sh '''
                docker build -t $AUTH_IMAGE:latest ./auth-service
                docker build -t $ORDER_IMAGE:latest ./order-service
                docker build -t $PRODUCT_IMAGE:latest ./product-service
            '''
        }
    }

    stage('Stop Existing Containers') {
        steps {
            echo '🛑 Stopping existing containers...'

            sh '''
                docker stop auth-container || true
                docker stop order-container || true
                docker stop product-container || true

                docker rm auth-container || true
                docker rm order-container || true
                docker rm product-container || true
            '''
        }
    }

    stage('Run Containers') {
        steps {
            echo '🚀 Starting containers...'

            sh '''
                docker run -d \
                    --name auth-container \
                    -p 3001:3001 \
                    $AUTH_IMAGE:latest

                docker run -d \
                    --name order-container \
                    -p 3002:3002 \
                    $ORDER_IMAGE:latest

                docker run -d \
                    --name product-container \
                    -p 3003:3003 \
                    $PRODUCT_IMAGE:latest
            '''
        }
    }

    stage('Verify Deployment') {
        steps {
            echo '🔍 Checking running containers...'

            sh '''
                docker ps

                echo "Testing Auth Service..."
                curl -f http://localhost:3001

                echo "Testing Order Service..."
                curl -f http://localhost:3002

                echo "Testing Product Service..."
                curl -f http://localhost:3003
            '''
        }
    }
}

post {

    success {
        echo '✅ Deployment successful!'

        emailext(
            subject: "SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
            body: """


Jenkins Build Successful!

Job: ${env.JOB_NAME}
Build: #${env.BUILD_NUMBER}
Status: SUCCESS

Docker deployment completed successfully.

Build URL: ${env.BUILD_URL}
""",
to: '[sasikala.bedre@gmail.com](mailto:sasikala.bedre@gmail.com)'
)
}


    failure {
        echo '❌ Deployment failed!'

        emailext(
            subject: "FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
            body: """


Jenkins Build Failed!

Job: ${env.JOB_NAME}
Build: #${env.BUILD_NUMBER}
Status: FAILED

Please check the Jenkins console output.

Build URL: ${env.BUILD_URL}
""",
to: '[sasikala.bedre@gmail.com](mailto:sasikala.bedre@gmail.com)'
)
}

```
    always {
        echo '📋 Jenkins deployment completed.'
    }
}


}
