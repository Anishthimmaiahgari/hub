pipeline {
    agent any
    
    // parameters {
    //     string(name: 'BRANCH_NAME', defaultValue: "main", description: 'Select a branch')
    // }
    
    environment {
        PROJECT_ID = 'modern-heading-440004-q8' 
        IMAGE_NAME_QA = "gcr.io/${PROJECT_ID}/reward_frontend_qa"
        IMAGE_NAME_UAT = "gcr.io/${PROJECT_ID}/reward_frontend_uat"
        IMAGE_NAME_PROD = "gcr.io/${PROJECT_ID}/reward_frontend_prod"
        SERVICE_ACCOUNT_JSON = credentials('gcp-serv-accnt')
    }

    stages {

        stage('Install Docker') {
            steps {
                script {
                    // Check if Docker is installed; install if not
                    def dockerInstalled = sh(script: 'command -v docker', returnStatus: true) == 0
                    if (!dockerInstalled) {
                        echo 'Docker not found. Installing Docker...'
                        sh '''
                            apt-get update
                            apt-get install -y apt-transport-https ca-certificates curl gnupg lsb-release
                            curl -fsSL https://download.docker.com/linux/$(. /etc/os-release; echo "$ID")/gpg | gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
                            echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/$(. /etc/os-release; echo "$ID") $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null
                            apt-get update
                            apt-get install -y docker-ce docker-ce-cli containerd.io
                            usermod -aG docker jenkins
                            newgrp docker // Apply permissions without logout
                        '''
                    } else {
                        echo 'Docker is already installed.'
                    }
                }
            }
        }

        stage('Auth and Docker Build for QA') {
            // when {
            //     branch 'main'
            // }
            steps {
                script {
                    sh '''
                        gcloud auth activate-service-account --key-file=${SERVICE_ACCOUNT_JSON}
                        gcloud config set project ${PROJECT_ID}
                        gcloud auth configure-docker --quiet
                        docker build -t ${IMAGE_NAME_QA} .
                        docker push ${IMAGE_NAME_QA}
                    '''
                }
            }
        }

        stage('Deploy to QA') {
            // when {
            //     branch 'main'
            // }
            steps {
                script {
                    sh '''
                        gcloud run deploy pyt-git-stack-qa --project ${PROJECT_ID} --image ${IMAGE_NAME_QA} --platform managed --region us-west1 --port=8080 --allow-unauthenticated --min-instances 1 --max-instances 2 --memory 3Gi
                    '''
                }
            }
        }

        // stage('Manual Approval for UAT') {
        //     when {
        //         branch 'las'
        //     }
        //     steps {
        //         script {
        //             def userApproval = input(
        //                 id: 'UserApproval', 
        //                 message: 'Do you approve to deploy to UAT?', 
        //                 parameters: [
        //                     choice(
        //                         name: 'ApprovalChoice', 
        //                         choices: ['Yes', 'Abort'], 
        //                         description: 'Choose whether to approve the deployment to UAT.'
        //                     )
        //                 ],
        //                 timeout: 30,  // Timeout after 30 minutes
        //                 timeoutMessage: 'User did not approve within the timeout period. Aborting.'
        //             )
        //             if (userApproval == 'Yes') {
        //                 echo 'User approved to proceed with deployment to UAT.'
        //             } else {
        //                 echo 'User did not approve. Aborting the pipeline.'
        //                 currentBuild.result = 'ABORTED'
        //                 error('User did not approve deployment to UAT. Aborting pipeline.')
        //             }
        //         }
        //     }
        // }
        // stage('Auth and Docker Build for UAT') {
        //     steps {
        //         script {
        //             sh '''
        //                 gcloud auth activate-service-account --key-file=${SERVICE_ACCOUNT_JSON}
        //                 gcloud config set project ${PROJECT_ID}
        //                 gcloud auth configure-docker --quiet
        //                 docker build -t ${IMAGE_NAME_UAT} .
        //                 docker push ${IMAGE_NAME_UAT}
        //             '''
        //         }
        //     }
        // }

//         stage('Deploy to UAT') {
//             steps {
//                 script {
//                     sh '''
//                         gcloud run deploy pyt-git-stack-uat --project ${PROJECT_ID} --image ${IMAGE_NAME_UAT} --platform managed --region us-west1 --port=8080 --allow-unauthenticated --min-instances 1 --max-instances 2 --memory 3Gi
//                     '''
//                 }
//             }
//         }

//         stage('Manual Approval for Prod') {
//             steps {
//                 script {
//                     def userApproval = input(
//                         id: 'UserApproval', 
//                         message: 'Do you approve to deploy to Prod?', 
//                         parameters: [
//                             choice(
//                                 name: 'ApprovalChoice', 
//                                 choices: ['Yes', 'Abort'], 
//                                 description: 'Choose whether to approve the deployment to Prod.'
//                             )
//                         ],
//                         timeout: 30,  // Timeout after 30 minutes
//                         timeoutMessage: 'User did not approve within the timeout period. Aborting.'
//                     )
//                     if (userApproval == 'Yes') {
//                         echo 'User approved to proceed with deployment to Prod.'
//                     } else {
//                         echo 'User did not approve. Aborting the pipeline.'
//                         currentBuild.result = 'ABORTED'
//                         error('User did not approve deployment to Prod. Aborting pipeline.')
//                     }
//                 }
//             }
//         }

//         stage('Auth and Docker Build for Prod') {
//             steps {
//                 script {
//                     sh '''
//                         gcloud auth activate-service-account --key-file=${SERVICE_ACCOUNT_JSON}
//                         gcloud config set project ${PROJECT_ID}
//                         gcloud auth configure-docker --quiet
//                         docker build -t ${IMAGE_NAME_PROD} .
//                         docker push ${IMAGE_NAME_PROD}
//                     '''
//                 }
//             }
//         }

//         stage('Deploy to Prod') {
//             steps {
//                 script {
//                     sh '''
//                         gcloud run deploy pyt-git-prod --project ${PROJECT_ID} --image ${IMAGE_NAME_PROD} --platform managed --region us-west1 --port=8080 --allow-unauthenticated --min-instances 1 --max-instances 2 --memory 3Gi
//                     '''
//                 }
//             }
//         }
//     }
// }
