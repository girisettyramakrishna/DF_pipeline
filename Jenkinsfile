pipeline {
    agent any

    environment {
        APP_DIR = "/srv/shiny-server/demand-forecasting"
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/girisettyramakrishna/DF_pipeline.git'
            }
        }

        stage('Install R Packages') {
            steps {
                sh '''
                    sudo Rscript -e "install.packages(c('shiny', 'forecast', 'ggplot2', 'rmarkdown', 'lubridate'), repos='https://cloud.r-project.org')"
                '''
            }
        }

        stage('Deploy App to Shiny Server') {
            steps {
                sh '''
                    sudo mkdir -p "$APP_DIR"
                    sudo cp -r ./app/* "$APP_DIR"/
                    sudo chown -R shiny:shiny "$APP_DIR"
                '''
            }
        }

        stage('Restart Shiny Server') {
            steps {
                sh 'sudo systemctl restart shiny-server'
            }
        }
    }

    post {
        success {
            echo "Deployment successful. App is live at: http://192.168.42.87:3838/demand-forecasting"
        }
        failure {
            echo "Deployment failed. Check Jenkins logs for error details."
        }
    }
}
