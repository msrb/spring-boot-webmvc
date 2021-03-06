#!/usr/bin/groovy
@Library('github.com/msrb/fabric8-pipeline-library@fast')

def failIfNoTests = ""
try {
  failIfNoTests = ITEST_FAIL_IF_NO_TEST
} catch (Throwable e) {
  failIfNoTests = "false"
}

def localItestPattern = ""
try {
  localItestPattern = ITEST_PATTERN
} catch (Throwable e) {
  localItestPattern = "*KT"
}


def versionPrefix = ""
try {
  versionPrefix = VERSION_PREFIX
} catch (Throwable e) {
  versionPrefix = "1.0"
}

def canaryVersion = "${versionPrefix}.${env.BUILD_NUMBER}"
def utils = new io.fabric8.Utils()
def label = "buildpod.${env.JOB_NAME}.${env.BUILD_NUMBER}".replace('-', '_').replace('/', '_')

mavenNode {
  git url: 'https://github.com/msrb/spring-boot-webmvc.git'
  if (utils.isCI()){
    
      mavenCI{}
    
  } else if (utils.isCD()){
    
    def envStage = utils.environmentNamespace('staging')

    echo 'NOTE: running pipelines for the first time will take longer as build and base docker images are pulled onto the node'
    
    container(name: 'maven') {
    
      stage 'Build Release'
      mavenCanaryRelease {
        version = canaryVersion
      }

      stage 'Rollout Staging'
      kubernetesApply(environment: envStage)

    }
  }
}



