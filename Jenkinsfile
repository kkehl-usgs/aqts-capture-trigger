@Library(value='iow-ecs-pipeline@1.0.0', changelog=false) _

pipeline {
    agent {
        node {
            label 'project:any'
        }
    }
    parameters {
        choice(choices: ['TEST', 'QA', 'PROD-EXTERNAL'], description: 'Deploy Stage (i.e. tier)', name: 'DEPLOY_STAGE')
    }
    stages {
        stage('run build the zip file for lambda') {
            agent {
                dockerfile true
            }
            steps {
                sh '''
                npm install serverless
                ./node_modules/serverless/bin/serverless plugin install -n serverless-python-requirements
                ./node_modules/serverless/bin/serverless deploy --stage ${DEPLOY_STAGE} --region us-west-2
                '''
            }
        }
    }
    post {
        always {
            script {
                pipelineUtils.cleanWorkspace()
            }
        }
        failure {
            script {
                pipelineUtils.sendEmailNotification(
                    to: 'ayan@usgs.gov',
                    attachLog: true
                )
            }
        }
    }
}
