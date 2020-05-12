
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

def deploy_helm() {
    helm("upgrade aplication Helm")
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

        stage('Deploy on K8s with Helm Chart') {
            agent { docker {
              image 'alpine/helm'
              args '--entrypoint='
            } }
            steps {
                deploy_helm()
            }
       }
    }
}

