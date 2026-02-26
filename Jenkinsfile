pipeline{
    agent { label 'built-in' }
    stages{
        stage("Environment Variable"){
            steps{
                echo "Setting Environment variables"
                sh "export PATH=$PATH:/usr/bin"
            }
        }
        stage("SCM"){
            steps{
                echo "Pulling changes from Git repository"
                git branch: 'main', url: 'https://github.com/pranaygupta1988/website1.git'
            }
        }
        stage("Docker Login"){
            steps{
                echo "Logging to docker"
                withCredentials([string(credentialsId: 'DOCKER_HUB_TOKEN', variable: 'DOCKER_HUB_TOKEN')]) {
                 sh "echo $DOCKER_HUB_TOKEN | docker login -u pranaygupta1988 --password-stdin"
                } 
            }
        }
        stage("Build"){
            steps{
                echo "Building image"
                sh "docker image build -t pranaygupta1988/website1 ."
            }
        }
        stage("Pushing the image"){
            steps{
                echo "Pushing the image"
                sh "docker image push pranaygupta1988/website1"
            }
        }
        stage("Deploy Service") {
            steps {
                sh '''
                if docker service inspect website1 > /dev/null 2>&1; then
                    echo "Service exists. Performing rolling update..."
                    docker service update \
		    --force \
                    --update-parallelism 1 \
                    --update-delay 10s \
                    --image pranaygupta1988/website1 \
                    website1
                else
                    echo "Service does not exist. Creating new service..."
                    docker service create \
                    --name website1 \
                    -p 8000:80 \
                    --replicas 2 \
                    pranaygupta1988/website1
                fi
                '''
   		 }
	  }
    }
}
