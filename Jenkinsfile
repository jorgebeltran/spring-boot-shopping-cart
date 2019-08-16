pipeline{
    agent none
    stages{
        stage("Clone Repository"){
            agent { label 'master' }
            steps{
                git 'https://github.com/jorgebeltran/spring-boot-shopping-cart.git'
                sh "echo Cloned!"
            }
            
        }
        stage("Build"){
            agent{
                docker 'maven:3-alpine'
            }
            steps{
                sh "mvn -q clean package"
            }
        }
        stage("Package"){
            agent { label ' master' }
            steps{
                sh "docker build -t shopping/cart ."
                sh "docker save -o cart.tar shopping/cart"
                stash name: "stash-artifact", includes: "cart.tar"
                archiveArtifacts 'target/*jar'
            }
        }
        stage("Archive artifact"){
            agent {label 'master'}
            steps{
                archiveArtifacts 'target/*jar'
            }
        }
        stage("Deployment PROD"){
            agent{label 'PROD'}
            steps{
                 unstash "stash-artifact"
                 sh "docker load -i blog.tar"
                 sh "docker rm blog -f || true"
                 sh "docker run -d -p 8090:8090 --name blog mauricio/blog"
            }
        }
    }
}

