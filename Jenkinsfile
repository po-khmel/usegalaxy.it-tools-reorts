pipeline {
    agent any

    options {
        buildDiscarder(logRotator(numToKeepStr: '5'))
    }

    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM',
                    branches: [[name: '*/main']],
                    doGenerateSubmoduleConfigurations: false,
                    extensions: [[$class: 'CleanCheckout']],
                    userRemoteConfigs: [[credentialsId: 'pokhmel_github_test_tools', url: 'git@github.com:po-khmel/usegalaxy-it-tools-reports.git']]
                ])
            }
        }

        stage('Build') {
            steps {
                deleteDir()
                // Setup ShiningPanda virtualenv
                withPythonEnv('Python-3.8.10') {
                    sh 'git checkout main'
                    sh 'mkdir reports-$(date +%Y-%m) || true'
                    sh 'POST="$(date +%Y-%m-%d-%H-%M)-tools-update.md"'
                    sh 'cat /home/ubuntu/workspace/usegalaxy.it-tools-test/report.log | python /home/ubuntu/workspace/usegalaxy.it-tools-test/scripts/generate-report.py > reports-$(date +%Y-%m)/${POST}'
                    sh 'git add .'
                    sh 'git commit -m "Jenkins Build: ${BUILD_NUMBER}"'
                }
            }
        }
    }

    post {
        success {
            git branch: 'main', credentialsId: 'pokhmel_github_test_tools', pushOnlyIfSuccess: true, url: 'git@github.com:po-khmel/usegalaxy-it-tools-reports.git'
        }
    }
}
