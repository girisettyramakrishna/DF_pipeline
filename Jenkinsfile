pipeline {
    agent any

    environment {
        R_LIBS_USER = '/usr/local/lib/R/site-library'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/girisettyramakrishna/DF_pipeline.git', branch: 'master'
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
                    sudo mkdir -p /srv/shiny-server/demand-forecasting
                    sudo cp -r app.R www forecast data.R forecast.R packages_to_be_installed.R /srv/shiny-server/demand-forecasting/
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
        failure {
            echo 'Deployment failed. Check Jenkins logs for error details.'
        }
        success {
            echo 'Deployment completed successfully.'
        }
    }
}
