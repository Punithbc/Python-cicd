pipeline {
 agent {
    docker {
      image 'abhishekf5/maven-abhishek-docker-agent:v1'
      args '--user root -v /var/run/docker.sock:/var/run/docker.sock' // mount Docker socket to access the host's Docker daemon
    }
  stages {
    stage('Checkout') {
      steps {
        sh 'echo passed'
        git branch: 'main', url: 'https://github.com/Punithbc/Python-cicd.git'
      }
    }
    stage('Static Code Analysis') {
      environment {
        SONAR_URL = "http://18.212.37.31:9000"
      }
      steps {
        withCredentials([string(credentialsId: 'MySonarToken', variable: 'SONAR_AUTH_TOKEN')]) {
          sh 'mvn sonar:sonar -Dsonar.login=$SONAR_AUTH_TOKEN -Dsonar.host.url=${SONAR_URL}'
        }
      }
    }
    stage('Build and Push Docker Image') {
      environment {
        DOCKER_IMAGE = "mechai/PYTHON-CICD:${BUILD_NUMBER}"
        // DOCKERFILE_LOCATION = "java-maven-sonar-argocd-helm-k8s/spring-boot-app/Dockerfile"
        REGISTRY_CREDENTIALS = credentials('docker-cred')
      }
      steps {
        script {
            sh 'docker build -t ${DOCKER_IMAGE} .'
            def dockerImage = docker.image("${DOCKER_IMAGE}")
            docker.withRegistry('https://index.docker.io/v1/', "docker-cred") {
                dockerImage.push()
            }
        }
      }
    }
//     stage('Update Deployment File') {
//         environment {
//             GIT_REPO_NAME = "Jenkins-Zero-To-Hero"
//             GIT_USER_NAME = "iam-veeramalla"
//         }
//         steps {
//             withCredentials([string(credentialsId: 'github', variable: 'GITHUB_TOKEN')]) {
//                 sh '''
//                     git config user.email "abhishek.xyz@gmail.com"
//                     git config user.name "Abhishek Veeramalla"
//                     BUILD_NUMBER=${BUILD_NUMBER}
//                     sed -i "s/replaceImageTag/${BUILD_NUMBER}/g" java-maven-sonar-argocd-helm-k8s/spring-boot-app-manifests/deployment.yml
//                     git add java-maven-sonar-argocd-helm-k8s/spring-boot-app-manifests/deployment.yml
//                     git commit -m "Update deployment image to version ${BUILD_NUMBER}"
//                     git push https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main
//                 '''
//             }
//         }
//   
//   }
}
}
}
