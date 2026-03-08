pipeline {
    // Run the pipeline on any available Jenkins agent/node
    agent {label "dev"}

    stages {

        // Stage 1: Clone the source code from GitHub repository
        stage("code clone") {
            steps {
                // Pull the master branch of the project into Jenkins workspace
                git branch: "master", url: "https://github.com/bdhanore26/two-tier-flask-app.git"
            }
        }

        // Stage 2: Build the Docker image for the Flask application
        stage("build the app") {
            steps {
                // Build Docker image named "myapp" from the Dockerfile
                // --no-cache ensures a fresh build so latest code changes are included
                sh "docker build -t myapp ."
            }
        }

        // Stage 3: Testing stage (placeholder)
        stage("test the app") {
            steps {
                // Currently only printing a message
                // In real projects, automated tests would run here
                echo "tests will be done by developers"
            }
        }

        // Stage 4: Login to DockerHub and push the built image
        stage("dockerhub login") {
            steps {
                // Retrieve DockerHub credentials securely from Jenkins credentials store
                withCredentials([usernamePassword(
                    credentialsId:"bhagwat",
                    passwordVariable:"admin",
                    usernameVariable:"docker"
                )]){

                // Login to DockerHub using stored credentials
                sh "docker login -u ${env.docker} -p ${env.admin}"

                // Tag the locally built image with DockerHub repository name
                sh "docker image tag myapp ${env.docker}/myapp:latest"

                // Push the Docker image to DockerHub registry
                sh "docker push ${env.docker}/myapp:latest"
            }
            }
        }

        // Stage 5: Deploy the updated application using Docker Compose
        stage("deploy the app") {
            steps {
                // Stop and remove existing containers
                sh "docker-compose down"

                // Pull the latest image from DockerHub
                sh "docker-compose pull"

                // Recreate containers in detached mode using updated image
                sh "docker-compose up -d --build "
            }
        }

    }
    post{
        success{
            emailext(
                subject: "build succced" ,
                body: "Good News : The build was succesful",
                to: 'bhagwatd2610@gmail.com'
                )
        }
        failure{
            emailext(
                subject:"build failed",
                body: "BAD  News : The buikld was Failed",
                to: 'bhagwatd2610@gmail.com'
                )
            
        }
    }
}
