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
            semgrep scan --config=auto \
            --exclude zap-report.html
        '''
      }
    }

    stage('Build & Deploy HTML') {
      steps {
        sh '''
          docker ps -q --filter "publish=8081" | xargs -r docker rm -f
          docker-compose down --remove-orphans
          docker-compose up -d --build
        '''
      }
    }

    stage('DAST - OWASP ZAP') {
      steps {
        sh '''
          docker run --rm --network host \
            --user root \
            -v "$PWD:/zap/wrk" \
            zaproxy/zap-stable zap-baseline.py \
            -t http://localhost:8081 \
            -r zap-report.html \
            -I
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
