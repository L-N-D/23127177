pipeline {
  agent any

  stages {

    stage('Checkout') {
        steps {
            git branch: 'main',
            url: 'https://github.com/L-N-D/23127177.git'
        }
    }


    stage('SAST - Semgrep') {
      steps {
        sh '''
          docker run --rm -v "$PWD:/src" semgrep/semgrep \
            semgrep scan --config=auto --error
        '''
      }
    }

    stage('Build & Deploy') {
      steps {
        sh '''
          docker-compose down
          docker-compose up -d --build
        '''
      }
    }

    stage('DAST - OWASP ZAP') {
      steps {
        sh '''
          docker run --rm --network host \
            owasp/zap2docker-stable zap-baseline.py \
            -t http://localhost:3000 \
            -r zap-report.html
        '''
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: 'zap-report.html'
    }
  }
}
