#!/usr/bin/groovy

@Library('github.com/arilivigni/fabric8-pipeline-library@master')
def canaryVersion = "1.0.${env.BUILD_NUMBER}"
def utils = new io.fabric8.Utils()

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
      stage('Build Image') {
        sh '''
          #git config user.name $(git log -n1 --format='%cn' <SHA> )
          #git config user.email $(git log -n1 --format='%ce' <SHA> )
          git config user.name arilivigni
          git config user.email arilivigni@gmail.com
        '''
        mavenCanaryRelease {
          version = canaryVersion
        }
      }
    }
  }
}

