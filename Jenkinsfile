pipeline {
    agent { label "slave1" }
    parameters{
        choice(name:'ENV',choices:['dev','test','prod','release']) 
    } 
    stages {
        stage('build') {
            steps {
                echo 'build'
                    if (params.ENV=="release"){
                         withCredentials([usernamePassword(credentialsId: 'iti-sys-admin-sohag-docker-cred', usernameVariable: 'USERNAME_SOHAG', passwordVariable: 'PASSWORD_SOHAG')]) {
                            sh '''
                                docker login -u ${USERNAME_SOHAG} -p ${PASSWORD_SOHAG}
                                docker build -t kareemelkasaby/bakehouseitisohag:v${BUILD_NUMBER} .
                                docker push kareemelkasaby/bakehouseitisohag:v${BUILD_NUMBER}
                                echo ${BUILD_NUMBER} > ../build.txt
                            '''
                        }
            }
                   else{            
                      echo "user choicesed ${params.ENV}"
                   }

            
        }
    }

        stage('deploy') {
            
            steps {
                echo 'deploy'
                    if (params.ENV=="dev"||params.ENV=="test"||params.ENV=="prod"){
                         withCredentials([file(credentialsId: 'iti-sys-admin-sohag-kubeconfig-cred', variable: 'KUBECONFIG_SOHAG')]) {
                            sh '''
                                export BUILD_NUMBER=cat$()../build.txt)
                                mv Deployment/deploy.yaml Deployment/tmp.yaml
                                cat Deployment/tmp.yaml | envsubst > Deployment/deploy.yaml
                                rm -f Deployment/tmp.yaml
                                kubectl apply -f Deployment --kubeconfig ${KUBECONFIG_SOHAG} -n ${ENV}
                            '''
                    }
                }
            }
        }
    }
    
