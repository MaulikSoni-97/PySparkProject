pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                powershell '''
                    Write-Host "=== Running Pipenv Sync ==="
                    pipenv --python python.exe sync
                '''
            }
        }

        stage('Test') {
            steps {
                powershell '''
                    Write-Host "=== Running Tests with Pytest ==="
                    pipenv run pytest
                '''
            }
        }

        stage('Package') {
            when {
                anyOf { branch "master"; branch "release" }
            }
            steps {
                powershell '''
                    Write-Host "=== Packaging Files into sbdl.zip ==="
                    if (Test-Path "PySparkProject.zip") { Remove-Item "PySparkProject.zip" -Force }
                    Compress-Archive -Path lib -DestinationPath "C:\\Users\\Dell\\PycharmProjects".zip -Force
                '''
            }
        }

        stage('Release') {
            when {
                branch "release"
            }
            steps {
                powershell '''
                    Write-Host "=== Releasing to QA Folder ==="

                    $qaFolder = "C:\\Users\\Dell\\PycharmProjects\\QA_Deployment"
                    if (!(Test-Path $qaFolder)) {
                        New-Item -ItemType Directory -Path $qaFolder | Out-Null
                    }

                    Copy-Item "C:\\Users\\Dell\\PycharmProjects\\sbdl.zip", log4j.properties, sbdl_main.py, sbdl_submit.sh, conf -Destination $qaFolder -Recurse -Force
                '''
            }
        }

        stage('Deploy') {
            when {
                branch "master"
            }
            steps {
                powershell '''
                    Write-Host "=== Deploying to Production Folder (Local Simulation) ==="

                    $prodFolder = "C:\\Users\\Dell\\PycharmProjects\\Prod_Deployment"
                    if (!(Test-Path $prodFolder)) {
                        New-Item -ItemType Directory -Path $prodFolder | Out-Null
                    }

                    Copy-Item "C:\\Users\\Dell\\PycharmProjects\\sbdl.zip", log4j.properties, sbdl_main.py, sbdl_submit.sh, conf -Destination $prodFolder -Recurse -Force
                '''
            }
        }
    }
}
