pipeline{
    agent any
    environment {
        PROJECT_ID = ''
        CLUSTER_NAME = 'cluster-1'
        LOCATION = 'asia-south2-a'
        CREDENTIALS_ID = 'jenkins-pro'
    }
    stages{
        stage('checkout git'){
            steps{
                 git branch: 'main', url: 'https://github.com/shivampateriya1/spring-petclinic.git'
            }
           
        }

        stage('Build App'){
            steps{
                sh './mvnw clean package'
            }
        post{
            success{
                junit 'target/surefire-reports/*.xml'
                archiveArtifacts "target/*.jar"
            } 
        }    
        }
       stage('Docker build'){
        steps{
            script{
                sh 'docker build -t shivampateriyaknoldus/petclinic:0.1 . '
            }
        }
       }
       stage('Docker login and push'){
        steps{
            withCredentials([string(credentialsId: 'dockersecret', variable: 'TOKEN')]) {
            sh 'docker login -u shivampateriyaknoldus -p $TOKEN'

            sh 'docker push shivampateriyaknoldus/petclinic:0.1'
        }

       }
       }


       stage('Deploy to kubernetes'){
        steps{
            
            step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'petclinic.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
		   echo "Deployment Finished ..."
        }
       }

    }
}

// download docker in your agent
// download kubectl in agent
//  download k8s plug and create serviceaccount