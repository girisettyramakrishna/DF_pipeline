pipeline {
    agent any

    environment {
        APP_NAME = "demand_forecasting"
        DEPLOY_DIR = "/srv/shiny-server/${APP_NAME}"
        GIT_REPO = "https://github.com/girisettyramakrishna/DF_pipeline.git"
    }

    stages {
        stage('Checkout') {
            steps {
                git url: "${GIT_REPO}", branch: 'master'
            }
        }

        stage('Install R Packages') {
            steps {
                sh '''
                    echo "Setting timeout for package installation..."
                    Rscript -e "options(timeout=600); 
                                if (file.exists('packages_to_be_installed.R')) {
                                    cat('Installing R packages from packages_to_be_installed.R...\n')
                                    source('packages_to_be_installed.R')
                                } else {
                                    cat('No packages_to_be_installed.R found. Skipping package installation.\n')
                                }"
                '''
            }
        }

        stage('Deploy App to Shiny Server') {
            steps {
                sh '''
                    echo "Cleaning old deployment..."
                    sudo rm -rf ${DEPLOY_DIR}
                    
                    echo "Creating new deployment directory..."
                    sudo mkdir -p ${DEPLOY_DIR}

                    echo "Copying files to deployment directory..."
                    sudo cp -r * ${DEPLOY_DIR}/

                    echo "Changing ownership to shiny user..."
                    sudo chown -R shiny:shiny ${DEPLOY_DIR}
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
            echo "Deployment successful. Access the app at: http://192.168.42.105:3838/${APP_NAME}/"
        }
        failure {
            echo "Deployment failed. Check logs for details."
        }
    }
}
