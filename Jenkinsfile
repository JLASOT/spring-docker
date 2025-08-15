// pipeline {
//   agent any
//   environment {
//     IMAGE_NAME = "demo-ci-cd:latest"
//   }
//   stages {
//     stage('Checkout') {
//       steps {
//         checkout scm
//       }
//     }
//     stage('Build & Test') {
//       steps {
//         sh 'mvn -B clean package'
//       }
//     }
//     stage('Build Docker Image') {
//       steps {
//         sh 'docker build -t $IMAGE_NAME .'
//       }
//     }
//     stage('Run Container') {
//       steps {
//         sh 'docker rm -f demo-ci-cd || true'
//         sh 'docker run -d --name demo-ci-cd -p 8080:8080 $IMAGE_NAME'
//       }
//     }
//   }
//   post {
//     always {
//       junit '**/target/surefire-reports/*.xml'
//       archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
//     }
//   }
// }

// pipeline {
//     agent any
//     environment {
//         IMAGE_NAME = "demo-ci-cd:latest"
//         VM_USER = "jenkins"
//         VM_HOST = "192.168.100.199"
//         VM_PASS = "soto1234"
//     }
//     stages {
//         stage('Checkout') {
//             steps {
//                 checkout scm
//             }
//         }
//         stage('Build & Test') {
//             steps {
//                 sh 'mvn -B clean package'
//             }
//         }
//         stage('Deploy to VM with Docker') {
//             steps {
//                 // Limpiar carpeta en la VM antes de copiar
//                 sh """
//                     sshpass -p "$VM_PASS" ssh -o StrictHostKeyChecking=no $VM_USER@$VM_HOST 'rm -rf /tmp/app'
//                 """
//                 // Subir el código a la VM
//                 sh '''
//                 sshpass -p "$VM_PASS" scp -o StrictHostKeyChecking=no -r . $VM_USER@$VM_HOST:/tmp/app
//                 '''
//                 // Ejecutar build y run en la VM
//                 sh '''
//                 sshpass -p "$VM_PASS" ssh -o StrictHostKeyChecking=no $VM_USER@$VM_HOST << EOF
//                     cd /tmp/app
//                     docker build -t $IMAGE_NAME .
//                     docker rm -f demo-ci-cd || true
//                     docker run -d --name demo-ci-cd -p 8080:8080 $IMAGE_NAME
//                 EOF
//                 '''
//             }
//         }
//     }
//     post {
//         always {
//             junit '**/target/surefire-reports/*.xml'
//             archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
//         }
//     }
// }

pipeline {
    agent any
    environment {
        IMAGE_NAME = "demo-ci-cd:latest"
        VM_USER = "jenkins"
        VM_PASS = "soto1234"
        VM_HOST = "192.168.100.199"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build & Test') {
            steps {
                sh 'mvn -B clean package'
            }
        }

        stage('Deploy to VM with Docker') {
            steps {
                script {
                    // Limpiar carpeta en la VM antes de copiar
                    sh """
                        sshpass -p "$VM_PASS" ssh -o StrictHostKeyChecking=no $VM_USER@$VM_HOST 'rm -rf /tmp/app'
                    """

                    // Subir el proyecto a la VM
                    sh """
                        sshpass -p "$VM_PASS" scp -o StrictHostKeyChecking=no -r . $VM_USER@$VM_HOST:/tmp/app
                    """

                    // Ejecutar comandos Docker en la VM
                    sh """
                        sshpass -p "$VM_PASS" ssh -o StrictHostKeyChecking=no $VM_USER@$VM_HOST 'cd /tmp/app && docker build -t $IMAGE_NAME . && docker rm -f demo-ci-cd || true && docker run -d --name demo-ci-cd -p 8080:8080 $IMAGE_NAME'
                    """
                }
            }
        }
    }

    post {
        always {
            junit '**/target/surefire-reports/*.xml'
            archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
        }
    }
}