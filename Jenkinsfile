pipeline {
  agent {
    kubernetes {
      yamlFile 'KubernetesPod.yaml'
    }
  }
  stages {

    stage('prepare') {
      steps {
        container('tools') {
          dir('project') {
            echo 'preparing the module'
            checkout([
              $class: 'GitSCM', 
              branches: [[name: '*/main']], 
              extensions: [[$class: 'SubmoduleOption',
                disableSubmodules: false,
                parentCredentials: false,
                recursiveSubmodules: false,
                reference: '',
                trackingSubmodules: false
            ]], 
              userRemoteConfigs: [[url: 'https://github.com/rsmaxwell/mqtt-rpc']]
            ])
            sh('./scripts/prepare.sh')
          }
        }
      }
    }

    stage('deploy') {
      steps {
        container('gradle') {
          dir('project') {
            echo 'building and deploying the module'
            sh('./scripts/deploy.sh')
          }
        }
      }
    }
  }
}
