pipeline {
    agent any
    
    environment {
        APP_DIR = "/srv/shiny-server/demand-forecasting"  // Location where the app will be deployed
    }
    
    stages {
        stage('Checkout') {
            steps {
                // Clone the repository from GitHub (public, no credentials needed)
                git 'https://github.com/girisettyramakrishna/DF_pipeline.git'
            }
        }
        
        stage('Install Dependencies') {
            steps {
                // Install necessary R packages (adjust if needed)
                sh 'Rscript -e "install.packages(c(\'shiny\', \'forecast\', \'ggplot2\'))"'
            }
        }

        stage('Deploy to Shiny Server') {
            steps {
                // Copy the app to Shiny Server directory
                sh 'sudo cp -r . /srv/shiny-server/demand-forecasting/'
                
                // Set the correct permissions for the app
                sh 'sudo chown -R shiny:shiny /srv/shiny-server/demand-forecasting/'
            }
        }
        
        stage('Restart Shiny Server') {
            steps {
                // Restart Shiny Server to reflect the new app
                sh 'sudo systemctl restart shiny-server'
            }
        }
    }
    
    post {
        always {
            // Actions after the pipeline run (e.g., clean up)
        }
    }
}
