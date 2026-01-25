pipeline {
    agent any

    environment {
        SOLUTION = 'APIGitHubAction.sln'
        PROJECT  = 'APIGitHubAction/APIGitHubAction.csproj'
        PUBLISH_DIR = 'publish'
        
        
        IIS_SITE = 'Demo.API'
        IIS_PATH = 'D:\\Publish'
        APP_POOL = 'Demo.API'
        
    }

    stages {

     

        
        triggers {
            githubPush()
        }

        stage('Restore') {
            steps {
                powershell '''
                    dotnet --version
                    dotnet restore $env:SOLUTION
                '''
            }
        }

        stage('Build') {
            steps {
                powershell '''
                    dotnet build $env:SOLUTION -c Release --no-restore
                '''
            }
        }

        stage('Publish') {
            steps {
                powershell '''
                    dotnet publish $env:PROJECT `
                        -c Release `
                        -o $env:PUBLISH_DIR

                    Write-Host "Publish output:"
                    Get-ChildItem $env:PUBLISH_DIR
                '''
            }
        }
        
        stage('Deploy to IIS') {
            steps {
                powershell '''
                    Import-Module WebAdministration
        
                    Write-Host "Stopping IIS Website..."
                    if (Test-Path IIS:\\Sites\\$env:IIS_SITE) {
                        Stop-Website $env:IIS_SITE
                    }
        
                    Write-Host "Stopping App Pool..."
                    if (Test-Path IIS:\\AppPools\\$env:APP_POOL) {
                        Stop-WebAppPool $env:APP_POOL
                    }
        
                    Start-Sleep -Seconds 3
        
                    Write-Host "Deploying files..."
                    if (!(Test-Path $env:IIS_PATH)) {
                        New-Item -ItemType Directory -Path $env:IIS_PATH
                    }
        
                    Copy-Item "$env:PUBLISH_DIR\\*" $env:IIS_PATH -Recurse -Force
        
                    Write-Host "Starting App Pool..."
                    if (Test-Path IIS:\\AppPools\\$env:APP_POOL) {
                        Start-WebAppPool $env:APP_POOL
                    }
        
                    Write-Host "Starting IIS Website..."
                    if (Test-Path IIS:\\Sites\\$env:IIS_SITE) {
                        Start-Website $env:IIS_SITE
                    }
        
                    Write-Host "Deploy IIS SUCCESS (.NET 8 safe)"
                '''
            }
        }

        
    }
}
