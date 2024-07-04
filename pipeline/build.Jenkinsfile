pipeline {
    agent any
    environment {
        NEXUS_CREDENTIALS_ID = credentials('nexus')
        NEXUS_URL = 'https://champion-rationally-maggot.ngrok-free.app'

    }
    stages {
        stage('Install Dependencies') {
            steps {
                sh '''
                   python -m venv venv
                   venv/bin/activate

                   # now packages should be installed from your Nexus pypi.org-central, not from the original pypi.org!
                   NEXUS_PYPI_CENTRAL="${NEXUS_URL}/repository/pypi.org-central/simple"
                   pip install --trusted-host ${NEXUS_URL} --index-url ${NEXUS_PYPI_CENTRAL}  -r requirements.txt
                '''
            }
        }
        stage('Run Unittest') {
            steps {
                sh '''
                  . venv/bin/activate
                  python -m unittest discover -s tests
                '''
            }
        }
        stage('Upload to pypi dev') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${NEXUS_CREDENTIALS_ID}", passwordVariable: 'NEXUS_PASSWORD', usernameVariable: 'NEXUS_USERNAME')]) {
                    sh """
                        . venv/bin/activate
                        twine upload --repository-url ${NEXUS_URL} -u $NEXUS_USERNAME -p $NEXUS_PASSWORD dist/*
                    """
                }
            }
        }
    }
    post {
        cleanup {
            cleanWs()
        }
    }
}