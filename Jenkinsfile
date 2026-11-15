pipeline {
    agent any

    environment {
        HOST = '127.0.0.1'
        PORT = '5000'
        APP_MODULE = 'app:app'
    }

    stages {
        stage('Checkout') {
            steps {
                echo '=== Étape 1 : Récupération du code depuis GitHub ==='
                checkout scmGit(
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[
                        credentialsId: 'github-token',
                        url: scm.getUserRemoteConfigs()[0].getUrl()
                    ]]
                )
            }
        }

        stage('Setup Virtual Environment') {
            steps {
                echo '=== Étape 2 : Configuration de l\'environnement virtuel Python ==='
                // Sur Windows PowerShell, on utilise 'bat' ou 'powershell'
                bat '''
                    python --version
                    python -m venv venv
                    call venv\\Scripts\\activate
                    python -m pip install --upgrade pip
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Run Tests') {
            steps {
                echo '=== Étape 3 : Exécution des tests avec pytest ==='
                bat '''
                    call venv\\Scripts\\activate
                    pytest --cov=app --cov-report=term-missing
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo '=== Étape 4 : Déploiement local de l\'application ==='
                bat '''
                    call venv\\Scripts\\activate
                    start /B python app.py > app.log 2>&1
                '''
            }
        }
    }

    post {
        always {
            echo 'Fin d\'exécution du pipeline.'
        }
        success {
            echo 'Le pipeline CI/CD a réussi avec succès !'
        }
        failure {
            echo 'Échec du pipeline. Veuillez vérifier les logs.'
        }
    }
}