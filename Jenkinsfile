pipeline {
  agent {
    kubernetes {
      yamlFile 'KubernetesPod.yaml'
    }
  }

  environment {
    FAMILY = 'linux'
    ARCHITECTURE = 'amd64'
  }


  stages {

    stage('prepare') {
      steps {
        container('tools') {
          dir('project') {
            echo "prepare the application (FAMILY=${env.FAMILY}, ARCH=${env.ARCHITECTURE})"
            checkout([
              $class: 'GitSCM',
              branches: [[name: '*/main']],
              userRemoteConfigs: [[url: 'https://github.com/rsmaxwell/diaries']],
              extensions: [
                [$class: 'SubmoduleOption',
                  disableSubmodules: false,
                  recursiveSubmodules: true,
                  parentCredentials: true,
                  reference: '',
                  trackingSubmodules: false
                ]
              ]
            ])
            sh('./scripts/prepare.sh')
          }
        }
      }
    }

    stage('deploy') {
      environment {
        GRADLE_USER_HOME = '/home/gradle/.gradle'
      }
      steps {
        container('gradle') {
          dir('project') {
            echo 'build JAR and deploy to Archiva'
            sh('./scripts/deploy.sh')
          }
        }
      }
    }

    stage('image') {
      steps {
        container('tools') {
          dir('project') {
            echo 'build docker image and publish to docker repository'
            sh('./scripts/image.sh')
          }
        }
      }
    }
  }
}
