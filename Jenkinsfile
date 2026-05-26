pipeline {
    agent any

    stages {
        stage('1 - Checkout') {
            steps {
                // משיכת הקוד העדכני מהריפוזיטורי ב-GitHub
                checkout scm
            }
        }

        stage('2 - Build') {
            steps {
                // בניית האימג'ים של הפרויקט לפי ההגדרות בקובץ docker-compose
                sh 'docker-compose build'
            }
        }

        stage('3 - Remove Old System') {
            steps {
                // עצירה ומחיקה של קונטיינרים ישנים שרצים כרגע
                sh 'docker-compose down'
            }
        }

        stage('4 - Run New System') {
            steps {
                // הרמת המערכת המעודכנת ברקע (detached mode)
                sh 'docker-compose up -d'
            }
        }
      stage('5 - Test') {
            steps {
                echo 'Waiting for services to start...'
                sh 'sleep 15'
                
                echo 'Testing if containers are running...'
                // בדיקה שהקונטיינרים למעלה ולא קרסו
                sh 'docker ps | grep backend'
                sh 'docker ps | grep frontend'

                echo 'Testing Backend (Port 5001)...'
                // בדיקה שה-API מגיב
                sh 'curl -f http://localhost:5001 || exit 1'
                
                echo 'Testing Frontend (Port 3000)...'
                // בדיקה שהאתר באוויר
                sh 'curl -f http://localhost:3000 || exit 1'
            }
        }

        stage('6 - Deploy (Push to Docker Hub)') {
            steps {
                echo 'Pushing images to Docker Hub...'
                sh 'docker push etty791/frontend:latest'
                sh 'docker push etty791/backend:latest'
            }
        }
    }
}
