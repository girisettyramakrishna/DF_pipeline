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
                // Checkout the repository code
                git url: "${GIT_REPO}", branch: 'master'
            }
        }

        stage('Install R Packages') {
            steps {
                // Install R packages (if required) from 'packages_to_be_installed.R'
                sh '''
                    if [ -f packages_to_be_installed.R ]; then
                        echo "Installing R packages from packages_to_be_installed.R..."
                        sudo Rscript packages_to_be_installed.R
                    else
                        echo "No packages_to_be_installed.R found. Skipping package installation."
                    fi
                '''
            }
        }

        stage('Deploy App to Shiny Server') {
            steps {
                // Deploy the app to Shiny Server directory
                sh '''
                    echo "Cleaning old deployment..."
                    sudo rm -rf ${DEPLOY_DIR}  # Clean old deployment files
                    
                    echo "Creating new deployment directory..."
                    sudo mkdir -p ${DEPLOY_DIR}  # Ensure directory is created

                    echo "Copying application files to deployment directory..."
                    sudo cp -r * ${DEPLOY_DIR}/  # Copy current repository files

                    echo "Changing ownership to 'shiny' user..."
                    sudo chown -R shiny:shiny ${DEPLOY_DIR}  # Ensure shiny user has ownership
                '''
            }
        }

        stage('Restart Shiny Server') {
            steps {
                // Restart Shiny Server to apply the changes
                sh 'sudo systemctl restart shiny-server'
            }
        }
    }

    post {
        success {
            // Notify successful deployment with the application URL
            echo "Deployment successful. Access app at: http://192.168.42.105:3838/${APP_NAME}/"
        }
        failure {
            // Notify failure in case of errors
            echo "Deployment failed. Check logs for details."
        }
    }
}
