node("master") {
    stage("Git checkout") {
        checkout scm
    }
    def imageName
    stage("Create Lambda Docker Image") {
        def lambdaImage = docker.build("198890578717.dkr.ecr.us-east-2.amazonaws.com/johanc/test:${BUILD_NUMBER}", "-f ./Dockerfile .")
        sh "aws ecr get-login-password --region us-east-2 | docker login --username AWS --password-stdin 198890578717.dkr.ecr.us-east-2.amazonaws.com"
        lambdaImage.push()
        def response = sh(returnStdout: true,
            script: """
            aws ecr describe-images \
                --repository-name johanc/test \
                --image-ids imageTag=${BUILD_NUMBER} --region us-east-2
            """)
        imageInfo = readJSON text: response
        imageName = lambdaImage.parsedId.userAndRepo + "@" + imageInfo.imageDetails[0].imageDigest
        echo imageName
    }
    stage("Create build Lambda + IAM role") {
        docker.image("node:lts").inside() {
            sh "npm ci"
            sh "npm run serverless -- deploy --stage johanc --region us-east-2 -v --image ${imageName}"
        }
    }
    node("aws_lambda"){
        stage("Run Build Steps") {
            echo "I'm running in a lambda! Woooooooo!"
            sh "env | grep 'AWS_LAMBDA_'"
        
            echo "Performing...some build operations"
        
            sh "jq --help"
        }
    }
}

