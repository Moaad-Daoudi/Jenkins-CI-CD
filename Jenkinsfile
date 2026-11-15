pipeline {
    agent any

    triggers {
        cron('*/5 * * * *') // Se déclenche toutes les 5 minutes
    }

    environment {
        HOST = '127.0.0.1'
        PORT = '5000'
        APP_MODULE = 'app:app'
    }

    stages {
        stage('Checkout') {
            steps {
                echo '=== Étape 1 : Récupération du code depuis GitHub ==='
                checkout scm
            }
        }

        stage('Setup Virtual Environment') {
            steps {
                echo '=== Étape 2 : Configuration de l\'environnement virtuel Python ==='
                bat '''
                    python --version
                    python -m venv venv
                    call venv\\Scripts\\activate
                    python -m pip install --upgrade pip
                    pip install -r requirements.txt
                    pip install pytest-cov
                '''
            }
        }

        stage('Run Tests') {
            steps {
                echo '=== Étape 3 : Exécution des tests avec pytest ==='
                bat '''
                    call venv\\Scripts\\activate
                    python -m pytest --cov=app --cov-report=term-missing
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
