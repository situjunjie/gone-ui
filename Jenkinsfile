pipeline {
  agent any

  options {
    disableConcurrentBuilds()
    timestamps()
    buildDiscarder(logRotator(numToKeepStr: '20'))
  }

  parameters {
    choice(
      name: 'BUILD_ENV',
      choices: ['test', 'stage', 'prod'],
      description: 'Frontend build environment. Maps to pnpm build:test|stage|prod.'
    )
    string(
      name: 'DEPLOY_DIR',
      defaultValue: '/data/www/gone-ui',
      description: 'Absolute target directory that serves or is mounted into your existing container.'
    )
    booleanParam(
      name: 'CLEAN_DEPLOY_DIR',
      defaultValue: true,
      description: 'Remove existing files in DEPLOY_DIR before publishing the new artifact.'
    )
    string(
      name: 'POST_DEPLOY_CMD',
      defaultValue: '',
      description: 'Optional shell command to run after deploy, e.g. docker exec ... nginx -s reload'
    )
  }

  environment {
    NODE_OPTIONS = '--max_old_space_size=8192'
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Resolve Build Settings') {
      steps {
        script {
          env.BUILD_SCRIPT = "build:${params.BUILD_ENV}"
          env.DIST_DIR = [
            test : 'dist-test',
            stage: 'dist-stage',
            prod : 'dist-prod'
          ][params.BUILD_ENV]

          if (!env.DIST_DIR) {
            error("Unsupported BUILD_ENV: ${params.BUILD_ENV}")
          }
        }

        sh '''
          set -eu
          echo "BUILD_ENV=${BUILD_ENV}"
          echo "BUILD_SCRIPT=${BUILD_SCRIPT}"
          echo "DIST_DIR=${DIST_DIR}"
          echo "DEPLOY_DIR=${DEPLOY_DIR}"
        '''
      }
    }

    stage('Install Dependencies') {
      steps {
        sh '''
          set -eu
          corepack enable
          pnpm install --frozen-lockfile
        '''
      }
    }

    stage('Build') {
      steps {
        sh '''
          set -eu
          pnpm "${BUILD_SCRIPT}"
          test -d "${DIST_DIR}"
        '''
      }
    }

    stage('Deploy Artifact') {
      steps {
        sh '''
          set -eu

          if [ -z "${DEPLOY_DIR}" ]; then
            echo "DEPLOY_DIR cannot be empty"
            exit 1
          fi

          case "${DEPLOY_DIR}" in
            /|/root|/home|/usr|/var|/etc|/bin|/sbin|/opt)
              echo "Refusing to deploy directly to protected top-level path: ${DEPLOY_DIR}"
              exit 1
              ;;
          esac

          mkdir -p "${DEPLOY_DIR}"

          if [ "${CLEAN_DEPLOY_DIR}" = "true" ]; then
            find "${DEPLOY_DIR}" -mindepth 1 -maxdepth 1 -exec rm -rf {} +
          fi

          if command -v rsync >/dev/null 2>&1; then
            rsync -av --delete "${DIST_DIR}/" "${DEPLOY_DIR}/"
          else
            cp -R "${DIST_DIR}/." "${DEPLOY_DIR}/"
          fi
        '''
      }
    }

    stage('Post Deploy Hook') {
      when {
        expression {
          return params.POST_DEPLOY_CMD?.trim()
        }
      }
      steps {
        sh '''
          set -eu
          echo "Running post deploy command..."
          sh -c "${POST_DEPLOY_CMD}"
        '''
      }
    }
  }

  post {
    success {
      echo "Deploy completed: ${params.BUILD_ENV} -> ${params.DEPLOY_DIR}"
    }
    failure {
      echo 'Pipeline failed. Review the stage logs above.'
    }
  }
}
