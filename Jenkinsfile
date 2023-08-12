pipeline {
    agent any

    environment {
        JENKINS_JOB_NAME = "${env.JOB_NAME}"
        JENKINS_WORKSPACE = "${WORKSPACE}"
    }

    parameters {
        string(name:'BrowserName', defaultValue: 'Chrome', description: 'Browser Name')
        string(name:'ExecutionType', defaultValue: 'Selenium-Grid', description: 'Execution Type')
        string(name:'groups', defaultValue: 'Regression', description: 'groups')
        string(name:'MODULE', defaultValue: 'testng_Landing.xml', description: 'Module')
    }

    stages {
        stage("Pull image from Docker-hub") {
            steps {
                sh "echo JOBNAME: ${JENKINS_JOB_NAME}"
                sh "echo WORKSPACE: ${JENKINS_WORKSPACE}"
                sh "docker pull rhuayhua/selenium-parabank"
            }
        }

        stage("Start Selenium-Grid") {
            steps {
                sh "docker-compose up -d hub chrome firefox"
            }
        }

        stage("Run Test Cases") {
            steps {
                sh "echo USER: `whoami`"
                sh "echo LIST FILES: `ls -lrt`"
                sh "docker-compose up parabank-module"
            }
        }
    }

    post {
        always {
            sh "echo DIRECTORY: `pwd`"
            sh "rsync -av /home/ubuntu/aws/output/allure-results ${JENKINS_WORKSPACE}/output"
            sh "rsync -av /home/ubuntu/aws/output/test-output ${JENKINS_WORKSPACE}/output"
            sh "rm -fR /home/ubuntu/aws/output/allure-results/*"
            sh "rm -fR /home/ubuntu/aws/output/test-output/*"
            archiveArtifacts artifacts: "output/**"
            allure includeProperties: false, jdk: '', results: [[path: 'output/allure-results']]
            sh "rm -fR ${JENKINS_WORKSPACE}/output/"
            sh "docker-compose down"
        }
    }

}