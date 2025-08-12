pipeline {
    agent any

    environment {
        PATH = "/usr/local/bin:${env.PATH}" // Add Node.js & npm path
    }

    stages {

        // ===== FRONTEND BUILD =====
        stage('Build Frontend') {
            steps {
                dir('STUDENTAPI-REACT') {
                    sh 'npm install'
                    sh 'npm run build'
                }
            }
        }

        // ===== FRONTEND DEPLOY =====
        stage('Deploy Frontend to Tomcat') {
            steps {
                sh '''
                TOMCAT_PATH="/Users/keertichandanthatavarthi/apache-tomcat-10.1.43/webapps/reactstudentapi"

                # Remove old frontend
                if [ -d "$TOMCAT_PATH" ]; then
                    rm -rf "$TOMCAT_PATH"
                fi

                # Create new folder and copy dist files
                mkdir -p "$TOMCAT_PATH"
                cp -R STUDENTAPI-REACT/dist/* "$TOMCAT_PATH/"
                '''
            }
        }

        // ===== BACKEND BUILD =====
        stage('Build Backend') {
            steps {
                dir('STUDENTAPI-SPRINGBOOT') {
                    sh 'mvn clean package'
                }
            }
        }

        // ===== BACKEND DEPLOY =====
        stage('Deploy Backend to Tomcat') {
            steps {
                sh '''
                TOMCAT_WEBAPPS="/Users/keertichandanthatavarthi/apache-tomcat-10.1.43/webapps"

                # Remove old WAR and exploded folder
                rm -f "$TOMCAT_WEBAPPS/springbootstudentapi.war"
                rm -rf "$TOMCAT_WEBAPPS/springbootstudentapi"

                # Copy new WAR
                cp STUDENTAPI-SPRINGBOOT/target/*.war "$TOMCAT_WEBAPPS/"
                '''
            }
        }

    }

    post {
        success {
            echo 'Deployment Successful!'
        }
        failure {
            echo 'Pipeline Failed.'
        }
    }
}
