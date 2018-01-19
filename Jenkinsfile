def APP_NAME = 'secure-image-api'
def TAG_NAMES = ['dev', 'test', 'prod']
def BUILD_CONFIG = APP_NAME
def IMAGESTREAM_NAME = APP_NAME

node {
  stage('build ' + BUILD_CONFIG) {
    echo "Building: " + BUILD_CONFIG
    openshiftBuild bldCfg: BUILD_CONFIG, showBuildLogs: 'true'
    
    // Don't tag with BUILD_ID so the pruner can do it's job; it won't delete tagged images.
    // Tag the images for deployment based on the image's hash
    IMAGE_HASH = sh (
      script: """oc get istag ${IMAGESTREAM_NAME}:latest -o template --template=\"{{.image.dockerImageReference}}\"|awk -F \":\" \'{print \$3}\'""",
      returnStdout: true).trim()
    echo ">> IMAGE_HASH: ${IMAGE_HASH}"
  }

  stage('deploy-' + TAG_NAMES[0]) {
    openshiftTag destStream: IMAGESTREAM_NAME, verbose: 'true', destTag: TAG_NAMES[0], srcStream: IMAGESTREAM_NAME, srcTag: "${IMAGE_HASH}"
  }
}