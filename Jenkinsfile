pipeline {
  agent any

  environment {
    REPORT_DIR = "target/dependency-check-report"
  }

  options {
    ansiColor('xterm')
    timestamps()
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main',
            url: 'https://github.com/shreyap-gif/security.git'
      }
    }

    stage('Build & Dependency-Check') {
      steps {
        // run maven verify which will run dependency-check plugin (configured in pom.xml)
        sh 'mvn -B -DskipTests verify'
      }
    }

    stage('Publish ODC Report') {
      steps {
        // Publish XML report into Jenkins (creates trend charts, links)
        dependencyCheckPublisher pattern: "${REPORT_DIR}/dependency-check-report.xml",
                                 skipNoReportFiles: false,
                                 stopBuild: false
      }
    }
  }

  post {
    success {
      echo "Build + OWASP Dependency-Check completed successfully."
    }
    failure {
      echo "Build failed — Dependency-Check plugin likely triggered failBuildOnCVSS (CVSS threshold exceeded)."
    }
    always {
      archiveArtifacts artifacts: "${REPORT_DIR}/**/*", fingerprint: true
    }
  }
}
