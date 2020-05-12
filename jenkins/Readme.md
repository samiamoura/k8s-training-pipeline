# Add this project on Jenkins

## run jenkins locally
```bash
cd jenkins && docker-compose up -d
```

Go to http://localhost:9085

to see the initialAdminPassword :
```bash
cd jenkins && docker-compose exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

select : "installer les plugins suggérés"

## add your secrets

You have to add several secret (use http://localhost:9085/credentials/store/system/domain/_/newCredentials) :
* ID : "DOCKER_HUB_CREDENTIAL" and set your Username / Password


## create the pipeline

Go to blue ocean, and click on Create Pipeline

## Update Jenkinsfile

You have to update Jenkinsfile to add your deployment code.

To use kubectl, you have to add a docker kubectl agent like this : 
```
        stage('My Kubectl exemple') {
            agent {
                docker {
                    image 'bitnami/kubectl:latest'
                    args '--entrypoint ""'
                }
            }
            steps {
                sh "kubectl ..."
            }
        }
```
