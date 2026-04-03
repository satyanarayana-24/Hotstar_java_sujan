pipeline {
    agent any

    environment {
        SONARQUBE_ENV = 'sq'
        DOCKER_IMAGE = "9397054542/doc_img_name"
        
         KUBECONFIG = "/var/lib/jenkins/.kube/config"
        EKS_CLUSTER = "mycluster"
        AWS_DEFAULT_REGION = 'ap-south-1'
        RECIPIENTS = 'satyanarayanag666@gmail.com'
    }

    stages {

        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/satyanarayana-24/Hotstar_java_sujan'
            }
        }
        stage('BUILD') {
        steps {
            sh 'mvn clean package -DskipTests'
        }
    }
        stage('JENKINS TO NEXUS') {
        steps {
          withMaven(jdk: 'jdk21', maven: 'maven3', traceability: true) {
             sh 'mvn deploy'
}
        }
    }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv("${SONARQUBE_ENV}") {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 1, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }


        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:latest .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerCred', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh '''
                    echo $PASS | docker login -u $USER --password-stdin
                    docker push $DOCKER_IMAGE:latest
                    docker logout
                    '''
                }
            }
        }
         stage('Build') {
            steps {
                echo "Building..."
            }
        }

        stage('Test') {
            steps {
                echo "Testing..."
            }
        }

        stage('Configure EKS Access') {
    steps {
        sh '''
            export PATH=$PATH:/usr/local/bin
            aws eks --region $AWS_DEFAULT_REGION update-kubeconfig --name $EKS_CLUSTER
            kubectl config current-context
        '''
    }
}
        
        stage('Deploy to EKS') {
            steps {
                sh '''     
                       
                kubectl apply -f deployment.yml
                kubectl apply -f service.yml
                '''
            }
        }
    }

    post {
        success {
            emailext(
                subject: "Jenkins Job '${env.JOB_NAME}' Success",
                body: "Good news! Job '${env.JOB_NAME}' (#${env.BUILD_NUMBER}) succeeded.\n\nCheck console output at ${env.BUILD_URL}",
                to: "${RECIPIENTS}"
            )
        }

        failure {
            emailext(
                subject: "Jenkins Job '${env.JOB_NAME}' Failed",
                body: "Alert! Job '${env.JOB_NAME}' (#${env.BUILD_NUMBER}) failed.\n\nCheck console output at ${env.BUILD_URL}",
                to: "${RECIPIENTS}"
            )
        }

    }
}







// pipeline {
//     agent any

//     environment {
//         SONARQUBE_ENV = 'sq'
//         DOCKER_IMAGE = "9397054542/doc_img_name"
        
//          KUBECONFIG = "/var/lib/jenkins/.kube/config"
//         EKS_CLUSTER = "myclusterr"
//         AWS_DEFAULT_REGION = 'ap-south-1'
//         RECIPIENTS = 'satyanarayanag666@gmail.com'
//     }

//     stages {

//         stage('Clone Repository') {
//             steps {
//                 git branch: 'main', url: 'https://github.com/satyanarayana-24/Hotstar_java_sujan'
//             }
//         }
//         stage('BUILD') {
//         steps {
//             sh 'mvn clean package -DskipTests'
//         }
//     }
//         stage('JENKINS TO NEXUS') {
//         steps {
//           withMaven(jdk: 'jdk21', maven: 'maven3', traceability: true) {
//              sh 'mvn deploy'
// }
//         }
//     }
//         stage('SonarQube Analysis') {
//             steps {
//                 withSonarQubeEnv("${SONARQUBE_ENV}") {
//                     sh 'mvn sonar:sonar'
//                 }
//             }
//         }

//         stage('Quality Gate') {
//             steps {
//                 timeout(time: 1, unit: 'MINUTES') {
//                     waitForQualityGate abortPipeline: true
//                 }
//             }
//         }


//         stage('Build Docker Image') {
//             steps {
//                 sh 'docker build -t $DOCKER_IMAGE:latest .'
//             }
//         }

//         stage('Push Docker Image') {
//             steps {
//                 withCredentials([usernamePassword(credentialsId: 'dockerCred', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
//                     sh '''
//                     echo $PASS | docker login -u $USER --password-stdin
//                     docker push $DOCKER_IMAGE:latest
//                     docker logout
//                     '''
//                 }
//             }
//         }
//          stage('Build') {
//             steps {
//                 echo "Building..."
//             }
//         }

//         stage('Test') {
//             steps {
//                 echo "Testing..."
//             }
//         }

//         stage('Deploy to EKS') {
//             steps {
//                 sh '''
                
//                  aws eks --region $AWS_REGION update-kubeconfig --name $EKS_CLUSTE
                
//                 kubectl apply -f deployment.yml
//                 kubectl apply -f service.yml
//                 '''
//             }
//         }
//     }

//     post {
//         success {
//             emailext(
//                 subject: "Jenkins Job '${env.JOB_NAME}' Success",
//                 body: "Good news! Job '${env.JOB_NAME}' (#${env.BUILD_NUMBER}) succeeded.\n\nCheck console output at ${env.BUILD_URL}",
//                 to: "${RECIPIENTS}"
//             )
//         }

//         failure {
//             emailext(
//                 subject: "Jenkins Job '${env.JOB_NAME}' Failed",
//                 body: "Alert! Job '${env.JOB_NAME}' (#${env.BUILD_NUMBER}) failed.\n\nCheck console output at ${env.BUILD_URL}",
//                 to: "${RECIPIENTS}"
//             )
//         }

//     }
// }
