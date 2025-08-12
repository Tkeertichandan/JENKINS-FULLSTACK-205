pipeline {
    agent any

    environment {
        // Add Node.js and Maven paths
        PATH = "/usr/local/bin:/Users/keertichandanthatavarthi/Softwares/apache-maven-3.9.11/bin:${env.PATH}"
        TOMCAT_HOME = "/Users/keertichandanthatavarthi/Softwares/apache-tomcat-10.1.43"
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
                FRONTEND_PATH="$TOMCAT_HOME/webapps/reactstudentapi"

                # Remove old frontend
                if [ -d "$FRONTEND_PATH" ]; then
                    rm -rf "$FRONTEND_PATH"
                fi

                # Create new folder and copy dist files
                mkdir -p "$FRONTEND_PATH"
                cp -R STUDENTAPI-REACT/dist/* "$FRONTEND_PATH/"
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
                WEBAPPS_PATH="$TOMCAT_HOME/webapps"

                # Remove old WAR and exploded folder
                rm -f "$WEBAPPS_PATH/springbootstudentapi.war"
                rm -rf "$WEBAPPS_PATH/springbootstudentapi"

                # Copy new WAR
                cp STUDENTAPI-SPRINGBOOT/target/*.war "$WEBAPPS_PATH/"
                '''
            }
        }

        // ===== RESTART TOMCAT =====
        stage('Restart Tomcat') {
            steps {
                sh '''
                $TOMCAT_HOME/bin/shutdown.sh || true
                sleep 3
                $TOMCAT_HOME/bin/startup.sh
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
