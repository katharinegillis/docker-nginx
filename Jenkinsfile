pipeline {
    environment {
        DISCORD_WEBHOOK_URL = credentials('discord-jenkins-webhook')
        IMAGE = "registry.ops.katiecordescodes.com/nginx"
        REGISTRY_CREDENTIALS = "docker-registry-jenkins"
        REGISTRY_URL = "https://registry.ops.katiecordescodes.com"
    }
    agent any
    stages {
        stage("Start") {
            steps {
                discordSend(webhookURL: DISCORD_WEBHOOK_URL, description: "Build started.", link: env.BUILD_URL, title: "${env.JOB_NAME} #${currentBuild.number}", result: "SUCCESS")
            }
        }
        stage("Build image") {
            steps {
                script {
                    docker.withRegistry(REGISTRY_URL, REGISTRY_CREDENTIALS) {
                        sh "docker-compose -f docker-compose.build.yml build"
                    }
                }
            }
        }
        stage("Upload image") {
            steps {
                script {
                    docker.withRegistry(REGISTRY_URL, REGISTRY_CREDENTIALS) {
                        sh "docker push $IMAGE"
                    }
                }
            }
        }
    }
    post {
        success {
            discordSend(webhookURL: DISCORD_WEBHOOK_URL, description: "Build completed. (${currentBuild.durationString})", link: env.BUILD_URL, result: currentBuild.currentResult, title: "${env.JOB_NAME} #${currentBuild.number}")
        }
        failure {
            discordSend(webhookURL: DISCORD_WEBHOOK_URL, description: "Build failed. (${currentBuild.durationString})", link: env.BUILD_URL, result: currentBuild.currentResult, title: "${env.JOB_NAME} #${currentBuild.number}")
        }
        aborted {
            discordSend(webhookURL: DISCORD_WEBHOOK_URL, description: "Build aborted. (${currentBuild.durationString})", link: env.BUILD_URL, result: currentBuild.currentResult, title: "${env.JOB_NAME} #${currentBuild.number}")
        }
    }
}