pipeline {
    agent any

    environment {
        DOTNET_ROOT = '/usr/share/dotnet'
        PROJECT_PATH = 'MyApp'
        SOLUTION = 'MyApp.sln'
        PUBLISH_DIR = 'MyApp/bin/Release/net8.0/publish'
        APP_DLL = 'MyApp.dll'
        PM2_APP_NAME = 'myapp'
    }

    stages {
        stage('Restore') {
            steps {
                sh 'dotnet restore $SOLUTION'
            }
        }
        stage('Build') {
            steps {
                sh 'dotnet build $SOLUTION --configuration Release --no-restore'
            }
        }
        stage('Test') {
            steps {
                sh 'dotnet test $SOLUTION --no-build --verbosity normal'
            }
        }
        stage('Publish') {
            steps {
                sh 'dotnet publish $PROJECT_PATH/$PROJECT_PATH.csproj --configuration Release --output $PUBLISH_DIR'
            }
        }
        stage('Deploy with PM2') {
            steps {
                sh '''
                    pm2 delete $PM2_APP_NAME || true
                    pm2 start $PUBLISH_DIR/$APP_DLL --name $PM2_APP_NAME --interpreter=dotnet
                    pm2 save
                '''
            }
        }
    }
    post {
        always {
            sh 'pm2 status'
        }
    }
}
