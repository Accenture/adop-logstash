//ldop-logstash/Jenkinsfile

pipeline {
    agent none

    stages {
        stage('pipeline-init') {
            agent any 

            steps {
                script {
                    STAGE = ""
                    STATUS = ""
                    CHANGED = "NO"

                    CHANGES = ""
                    SUBJECT = ""

                    COLOR = ""
                }
            }
        }
        stage('hadolint-lint') {
            agent {
                docker {
                    image "lukasmartinelli/hadolint"
                    args "-u root"
                }
            }
            steps {
                script { CHANGED = "NO" }
                sh 'hadolint Dockerfile || true'
            }       
            post {
                success {
                    script { STATUS = "SUCCESS" }
                }
                failure {
                    script { STATUS = "FAILURE" }
                }
                changed {
                    script { CHANGED = "YES" }
                }
                always { script { SUBJECT = "Build #${env.BUILD_NUMBER} of ${env.JOB_NAME} at 'hadolint-lint'" } }
            }
        }
        stage('dockerlint-lint') {
            agent {
                docker {
                    image "redcoolbeans/dockerlint"
                }
            }
            steps {
                script { CHANGED = "NO" }
                sh 'dockerlint -f Dockerfile || true'
            }
            post {
                success {
                    script { STATUS = "SUCCESS" }
                }
                failure {
                    script { STATUS = "FAILURE" }
                }
                changed {
                    script { CHANGED = "YES" }
                }
                always { script { SUBJECT = "Build #${env.BUILD_NUMBER} of ${env.JOB_NAME} at 'dockerlint-lint'" } }
            }
        }
        stage('dockerfile-lint') {
            agent {
                docker {
                    image "projectatomic/dockerfile-lint"
                    args "-u root"
                }
            }
            steps {
                script { CHANGED = "NO" }
                sh 'dockerfile_lint -f Dockerfile || true'
            }
            post {
                success {
                    script { STATUS = "SUCCESS" }
                }
                failure {
                    script { STATUS = "FAILURE" }
                }
                changed {
                    script { CHANGED = "YES" }
                }
                always { script { SUBJECT = "Build #${env.BUILD_NUMBER} of ${env.JOB_NAME} at 'dockerfile-lint'" } }
            }
        }
        stage('ldop-logstash-validate') {
            agent any
            steps {
                sh "git pull origin ${env.BRANCH_NAME}"
                sh "echo \$(git tag -l | sort -V | tail -1) > result"
                script {
                    TAG = readFile 'result'
                    TAG = TAG.trim()
  
                    CHANGED = "NO"
                  
                    if (!(TAG ==~ /^[0-9]+\.[0-9]+\.[0-9]+$/)) {
                        error("Invalid Git tag format! Aborting...")
                    }

                    if (doesVersionExist('liatrio', 'ldop-logstash', "${TAG}")) {
                        error("LDOP Liatrio version already exists! Aborting...")
                    }
                }
            }
            post {
                success {
                    script { STATUS = "SUCCESS" }
                }
                failure {
                    script { STATUS = "FAILURE" }
                }
                changed {
                    script { CHANGED = "YES" }
                }
                always { script { SUBJECT = "Build #${env.BUILD_NUMBER} of ${env.JOB_NAME} at 'ldop-logstash-validate'" } }
            }
        }
        stage('ldop-logstash-build') {
            agent any
            steps {
                script { CHANGED = "NO" }
                sh "docker build -t liatrio/ldop-logstash:${env.BRANCH_NAME} ."
                sh "docker push liatrio/ldop-logstash:${env.BRANCH_NAME}"
            }
            post {
                success {
                    script { STATUS = "SUCCESS" }
                }
                failure {
                    script { STATUS = "FAILURE" }
                }
                changed {
                    script { CHANGED = "YES" }
                }
                always { script { SUBJECT = "Build #${env.BUILD_NUMBER} of ${env.JOB_NAME} at 'ldop-logstash-build'" } }
            }
        }
        stage('ldop-integration-testing') {
            agent {
              docker {
                image "hashicorp/terraform:full"
                args "-u root"
              }
            }
            steps {
                git branch: 'master', url: 'https://github.com/liatrio/ldop-docker-compose'
                sh "echo \$(pwd) > result"
                script {
                  DIR = readFile 'result'
                  DIR = DIR.trim()

                  CHANGED = "NO"
                }
                testSuite("ldop-logstash", "${TAG}", "${DIR}")
                sh "export TF_VAR_branch_name=\"${env.BRANCH_NAME}\""
                sh '''sed -i 's/timeout 30m/timeout -t 1800/g' test/integration/run-integration-test.sh &&
                      bash test/validation/validation.sh &&
                      cd test/integration/ &&
                      terraform init &&
                      bash run-integration-test.sh'''
            }
            post {
                success {
                    script { STATUS = "SUCCESS" }
                }
                failure {
                    script { STATUS = "FAILURE" }
                }
                changed {
                    script { CHANGED = "YES" }
                }
                always { script { SUBJECT = "Build #${env.BUILD_NUMBER} of ${env.JOB_NAME} at 'ldop-integration-testing'" } }
            }
        }
        stage('ldop-image-deploy') {
            agent any
            steps {
                script { CHANGED = "NO" }
                sh "docker tag liatrio/ldop-logstash:${env.BRANCH_NAME} liatrio/ldop-logstash:${TAG}"
                sh "docker push liatrio/ldop-logstash:${TAG}"
            }
            post {
                success {
                    script { STATUS = "SUCCESS" }
                }
                failure {
                    script { STATUS = "FAILURE" }
                }
                changed {
                    script { CHANGED = "YES" }
                }
                always { script { SUBJECT = "Build #${env.BUILD_NUMBER} of ${env.JOB_NAME} at 'ldop-image-deploy'" } }
            }
        }
    }
    post {
        always {
            script {
                if (CHANGED == "YES") {
                    if (STATUS == "SUCCESS") {
                        COLOR = "00FF00"
                    } else {
                        COLOR = "FF0000"
                    }

                    RESULT = formatSlackOutput(SUBJECT, env.JOB_URL, currentBuild.changeSets, STATUS)

                    slackSend (color: "#${COLOR}", message: "${RESULT}")
                }
            }
        }
    }
}
