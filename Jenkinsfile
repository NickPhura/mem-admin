// Edit your app's name below
def APP_NAME = 'mem-mmt'

// Edit your environment TAG names below
def TAG_NAMES = ['dev', 'test', 'prod']

// You shouldn't have to edit these if you're following the conventions
def BUILD_CONFIG = APP_NAME + '-build'
def IMAGESTREAM_NAME = APP_NAME

node {
  notifyStarted()
  try {
    stage('build ' + BUILD_CONFIG) {
      echo "Building: " + BUILD_CONFIG
      openshiftBuild bldCfg: BUILD_CONFIG, showBuildLogs: 'true'
      openshiftTag destStream: IMAGESTREAM_NAME, verbose: 'true', destTag: '$BUILD_ID', srcStream: IMAGESTREAM_NAME, srcTag: 'latest'
    }
    stage('deploy-' + TAG_NAMES[0]) {
      openshiftTag destStream: IMAGESTREAM_NAME, verbose: 'true', destTag: TAG_NAMES[0], srcStream: IMAGESTREAM_NAME, srcTag: '$BUILD_ID'
    }
    stage('deploy-' + TAG_NAMES[1]) {
      input "Deploy to " + TAG_NAMES[1] + "?"
      openshiftTag destStream: IMAGESTREAM_NAME, verbose: 'true', destTag: TAG_NAMES[1], srcStream: IMAGESTREAM_NAME, srcTag: '$BUILD_ID'
    }
  //  stage('deploy-'  + TAG_NAMES[2]) {
  //    input "Deploy to " + TAG_NAMES[2] + "?"
  //    openshiftTag destStream: IMAGESTREAM_NAME, verbose: 'true', destTag: TAG_NAMES[2], srcStream: IMAGESTREAM_NAME, srcTag: '$BUILD_ID'
  //  }
    notifySuccessful()
  } catch (e) {
    currentBuild.result = "FAILED"
    notifyFailed()
    throw e
  }
}

def notifyStarted() {
  slackSend (color: '#FFFF00', message: "STARTED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
}
def notifySuccessful() {
  slackSend (color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
}
def notifyFailed() {
  slackSend (color: '#FF0000', message: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
}