def buildAndPushDocker(workingdir, appname) {
    withCredentials([usernamePassword(credentialsId: 'DOCKER_HUB_CREDENTIAL', passwordVariable: 'DOCKER_HUB_PWD', usernameVariable: 'DOCKER_HUB_LOGIN')]) {
        sh "cd $workingdir && docker build -t $appname . && " +
                "docker tag $appname $DOCKER_HUB_LOGIN/$appname:latest && " +
                "docker login --username $DOCKER_HUB_LOGIN --password $DOCKER_HUB_PWD && " +
                "docker push $DOCKER_HUB_LOGIN/$appname:latest"
    }
}

def kubectl(opt, namespace) {
    withCredentials ( [string(credentialsId: 'K8S_TOKEN', variable: 'K8S_TOKEN')] ) {
        sh "export KUBERNETES_MASTER=https://104.199.68.113 &&  kubectl --insecure-skip-tls-verify=true --token='$K8S_TOKEN' $opt --namespace $namespace "
    }
}

def deploy() {
    kubectl('apply -f k8s', 'sami')
}

def helm(opt) {
    withCredentials([file(credentialsId: 'CONFIG_K8S', variable: 'CONFIG_K8S')]) {
        sh "helm --kubeconfig $CONFIG_K8S $opt"
    }
}

def action_helm(opt, chart) {
    helm("$opt application $chart")
}

pipeline {
    agent none
    stages {
        stage("Build Docker") {
            parallel {
                stage('database') {
                    agent any
                    steps {
                        buildAndPushDocker('bdd', 'bdd-mysql-k8s')
                    }
                }
                stage('api') {
                    agent any
                    steps {
                        buildAndPushDocker('tutoapi', 'api-k8s')
                    }
                }
            }
        }

        /*
        stage('Deploy on K8s') {
            agent { docker {
              image 'bitnami/kubectl'
              args '--entrypoint='
            } }
            steps {
                deploy()
            }
        }*/

        stage('Deploy on K8s with Helm') {
            agent { docker {
              image 'alpine/helm'
              args '--entrypoint='
            } }

            steps {
              action_helm('install', 'Helm')
            }
        }

        stage('Ensure api is deployed and test it') {
            agent any
            stages {

              stage("Ensure application is deploy") {
                steps {
                  echo "Ensure application is deploy"
                  sh label: '', script: 'timeout 300 bash -c \'while [[ "$(curl -s -o /dev/null -w \'\'%{http_code}\'\' http://api-sami.formationk8.projet-davidson.fr)" != "200" ]]; do sleep 5; done\' || false'
                }
              }

             stage("Install ansible role dependencies") {
                steps {
                  def result = sh label: '', returnStdout: true, script: 'curl http://api-sami.formationk8.projet-davidson.fr'

                  if (result !== "Hello you!"){
                    sh "exit 1"
                  }
                }
             }
          }
        }


        /*
        stage('Undeploy application') {
            agent { docker {
              image 'alpine/helm'
              args '--entrypoint='
            } }

            steps {
              action_helm('uninstall','')
            }
        }*/
    }
}
