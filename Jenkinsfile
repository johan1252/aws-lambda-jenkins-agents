node("master") {
    checkout scm
    stage("Create Lambda Docker Image") {
        def lambdaImage = docker.build("lambda-build-node-image", "-f ./lambda/Dockerfile .")
        lambdaImage.push()
        echo lambdaImage.id
    }

    docker.image("node:lts").inside() {
        sh "npm ci"
        sh "sls --help"
    }
}
