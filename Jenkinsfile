pipeline {
    agent any

    environment {
        APP_DIR = "/srv/shiny-server/demand-forecasting"
        RSCRIPT = "/usr/bin/Rscript"
        R_LIBS_USER = "${HOME}/R/library"
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
                    mkdir -p "$R_LIBS_USER"
                    $RSCRIPT -e "install.packages(c('shiny', 'forecast', 'ggplot2', 'rmarkdown', 'lubridate'), lib=Sys.getenv('R_LIBS_USER'), repos='https://cloud.r-project.org')"
                '''
            }
        }

        stage('Deploy App to Shiny Server') {
            steps {
                sh '''
                    # Use only contents inside the app directory (assumes your app is inside a folder)
                    sudo mkdir -p "$APP_DIR"
                    sudo cp -r ./* "$APP_DIR"/
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
            echo " Deployment successful. App is live at: http://localhost:3838/demand-forecasting"
        }
        failure {
            echo " Deployment failed. Check Jenkins logs for error details."
        }
    }
}
