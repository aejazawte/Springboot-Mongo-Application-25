pipeline
{
	agent any
	
    tools
	{
		maven 'Maven_3.8.6'
    }
	
    environment
    {
		Build_Number = "${BUILD_NUMBER}"
    }
	
	stages
	{
		stage('Checkout') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/aejazawte/Springboot-Mongo-Application-25.git',
                    credentialsId: 'github'
            }
        }
		
		stage('Build Project')
		{
			steps()
			{
				sh 'mvn clean package'
			}
		}
		
		stage('Build Docker Image')
		{
			steps()
			{
				sh "docker build -t admaejaz/java-maven-application:$Build_Number ."
			}
		}
		
		stage('Docker Build & Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh "docker build -t admaejaz/java-maven-application:${Build_Number} ."
                    sh "docker push admaejaz/java-maven-application:${Build_Number}"
                }
            }
        }
		
		stage("Update Image Tag in Kubernetes Manifest")
		{
			steps()
            {
				//Build_Tag - To Be Added in Manifest File RegistryURL/java-maven-application:Build_Tag
				//Build_Number - Parameterised Build Number.
				sh "sed -i 's/Build_Tag/${Build_Number}/g' SpringBootMongo.yaml"
            }
        }
		
		stage('Deploy Application in EKS Kubernetes Cluster')
		{
			steps()
			{
				sh 'kubectl apply -f SpringBootMongo.yaml'
				//sh 'kubectl delete deployment springbootmongo-deployment -n prod|| true'
				//sh 'kubectl apply -f SpringBootMongo.yaml'
				sh "kubectl -n prod rollout status deployment/springboot-deployment --timeout=120s"
			}
		}
	}
}