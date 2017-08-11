node {
    def app

    stage('Clone repository') {
        checkout scm
    }

    stage('Build image') {

        app = docker.build("harad/codechan")
    }

    stage('Test image') {
        /* Run tests against our image. */

        app.inside {
            sh 'python tests.py'
        }
    }

    stage('Push image') {

        docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }
    }

    stage('Deploy') {
        sh 'ssh root@52.31.118.85 docker pull harad/codechan'
        sh 'ssh root@52.31.118.85 docker stop codechan'
        sh 'ssh root@52.31.118.85 docker rm codechan'
        sh 'ssh root@52.31.118.85 docker run -d --name codechan --restart=always -p 80:5000 harad/codechan'
    }
}

