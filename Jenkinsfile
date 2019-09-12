pipeline {
  agent { label 'debian' }
  
  environment {
    DISTRIBUTION = 'buster'
    ARCH = 'amd64'
    BASEPATH = "/var/cache/pbuilder/base-${env.DISTRIBUTION}-${env.ARCH}.cow"
    BUILDRESULT = "${env.WORKSPACE}/result"
    SRC_DIR = """${sh(
      returnStdout: true,
      script: '/bin/ls -d wpa-supplicant-*'
    )}"""
  }
  
  stages {
    stage('Initialization') {
      steps {
        sh label: 'Update build environment', script: 'sudo cowbuilder update --basepath="${BASEPATH}"'
      }
    }
    
    stage('Build') {
      steps {
        dir(env.SRC_DIR) {
          sh label: 'Tag build in changelog',
             script: 'dch --local "+${BUILD_NUMBER}~" \
                          --distribution "unstable" \
                          "Build ${BUILD_NUMBER}"
          
          sh label: 'Build package',
             script: 'pdebuild --pbuilder cowbuilder \
                               --buildresult "${BUILDRESULT}" \
                               -- \
                               --basepath "${BASEPATH}"
        }
      }
    }
  }
  
  post {
    success {
      archiveArtifacts artifacts: 'result/*'
    }
  }
}
