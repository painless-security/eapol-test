pipeline {
  // Select build agent. This requires label 'debian'.
  agent { label 'debian' }
  
  // Sets environment for shell commands; available to Groovy script in env.*
  environment {
    DISTRIBUTION = 'buster'
    ARCH = 'amd64'

    // n.b., the substitutions in the next lines are in Groovy, not shell script.
    BASEPATH = "/var/cache/pbuilder/base-${env.DISTRIBUTION}-${env.ARCH}.cow"
    BUILDRESULT = "${env.WORKSPACE}/result"

    // Find the source directory dynamically so we can use a dir() step later
    SRC_DIR = '''${sh(
      returnStdout: true,
      script: '/bin/ls -d wpa-supplicant-*'
    )}'''.trim()
  }
  
  // Define build stages. These run sequentially.
  stages {
    stage('Initialization') {
      steps {
        sh label: 'Update build environment', script: 'sudo cowbuilder update --basepath="${BASEPATH}"'
        
        // Without this, pdebuild creates the result directory as root, which
        // prevents subsequent builds from cleaning the workspace.
        sh label: 'Create result directory', script: 'mkdir -p "${BUILDRESULT}"'
      }
    }
    
    stage('Build') {
      steps {
        dir(env.SRC_DIR) {
          sh label: 'Tag build in changelog',
             script: '''dch --local "+${BUILD_NUMBER}~" \
                            --distribution "unstable" \
                            "Build ${BUILD_NUMBER}"'''
          
          sh label: 'Build package',
             script: '''pdebuild --pbuilder cowbuilder \
                                 --buildresult "${BUILDRESULT}" \
                                 -- \
                                 --basepath "${BASEPATH}"'''
        }
      }
    }
  }
  
  // Steps to run following a build
  post {
    // Run only after successful build
    success {
      archiveArtifacts artifacts: 'result/*'
    }
  }
}
