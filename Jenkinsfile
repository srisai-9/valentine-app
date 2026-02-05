pipeline {
  agent any

  environment {
    IMAGE_NAME = "valentine-app"
    TAG = "${env.BUILD_NUMBER}"
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Python Lint (basic)') {
      steps {
        sh '''
          python3 --version || true
          pip3 install --user -r requirements.txt
          python3 -m py_compile app.py
        '''
      }
    }

    stage('Docker Build') {
      steps {
        sh '''
          docker --version
          docker build -t ${IMAGE_NAME}:${TAG} .
        '''
      }
    }

    stage('Run Container (smoke test)') {
      steps {
        sh '''
          docker rm -f valentine_test || true
          docker run -d --name valentine_test -p 5050:5000 ${IMAGE_NAME}:${TAG}
          sleep 2
          curl -f http://localhost:5050/ >/dev/null
          curl -f http://localhost:5050/happy >/dev/null
          docker rm -f valentine_test
        '''
      }
    }
  }

  post {
    always {
      sh 'docker images | head -n 5 || true'
    }
  }
}
