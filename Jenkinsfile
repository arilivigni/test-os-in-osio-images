#!/usr/bin/groovy
@Library('github.com/arilivigni/fabric8-pipeline-library@master')
def canaryVersion = "1.0.${env.BUILD_NUMBER}"
def utils = new io.fabric8.Utils()
def gitUser = config.gitUser
def gitEmail = config.gitEmail

mavenNode {
  checkout scm
  if (utils.isCI()) {

    mavenCI {
        integrationTestCmd =
             "mvn org.apache.maven.plugins:maven-failsafe-plugin:integration-test \
                org.apache.maven.plugins:maven-failsafe-plugin:verify \
                -Dnamespace.use.current=false -Dnamespace.use.existing=${utils.testNamespace()} \
                -Dit.test=*IT -DfailIfNoTests=false -DenableImageStreamDetection=true \
                -P openshift-it"
    }

  } else if (utils.isCD()) {
    echo 'NOTE: running pipelines for the first time will take longer as build and base docker images are pulled onto the node'
    container(name: 'maven', shell:'/bin/bash') {
        sh '''
            #git config --global user.name arilivigni
            #git config --global user.email arilivigni@gmail.com
            git config --global user.name ${gitUser}
            git config --global user.email ${gitEmail}
            ls -la /root
            env
      '''
      stage('Build Image') {
        mavenCanaryRelease {
          version = canaryVersion
        }
      }
    }
  }
}

