pipeline {
    agent any
    environment {
        USER = credentials('github-user')
        PASS = credentials('github-pass')
        GITHUB_REPO_SERVER = "ghcr.io"
        GITHUB_REPO_URL = "${GITHUB_REPO_SERVER}/${USER}"
        VERSION = "1.0.1"
    }
    stages {
        stage('docker login'){
            steps{
                script{
                    sh "echo $PASS | docker login -u $USER --password-stdin ${GITHUB_REPO_SERVER}"
                }
            }
        }
        stage('Build and Publish') {
            parallel {
                stage('build and publish ui'){
                    steps{
                        script{
                            sh "docker build -t ${GITHUB_REPO_URL}/kanban-ui:${VERSION} ./kanban-ui/ "
                            sh "docker push ${GITHUB_REPO_URL}/kanban-ui:${VERSION}"
                        }
                    }
                }
                stage('build and publish app'){
                    steps{
                        script{
                            sh "docker build -t ${GITHUB_REPO_URL}/kanban-app:${VERSION} ./kanban-app/ "
                            sh "docker push ${GITHUB_REPO_URL}/kanban-app:${VERSION}"
                        }
                    }
                }
            }
        }
        stage('build and package helm charts'){
            steps{
                script{
                    sh "helm dep up helm-charts/kanban-app"
                    sh "helm dep up helm-charts/kanban-ui"
                    sh "helm lint helm-charts/kanban-app"
                    sh "helm lint helm-charts/kanban-ui"
                    sh "helm dep up helm/"
                    sh "helm lint helm/"
                    sh "helm package helm/"
                }
            }
        }
    }
}