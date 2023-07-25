def imageName = 'paulappz/quote-service'
def registry = '570942461061.dkr.ecr.eu-west-2.amazonaws.com' 
def region = 'eu-west-2'

node('workers'){
    stage('Checkout'){
        checkout scm
    }
    
    stage('Unit Tests'){
        // def imageTest= docker.build("${imageName}-test", "-f Dockerfile.test .")
        //imageTest.inside{
        //    sh "python test_main.py"
        //}
    }
    
    stage('Build'){
       sh "aws ecr get-login-password --region ${region} | docker login --username AWS --password-stdin ${registry}/${imageName}"
        if (env.BRANCH_NAME == 'develop') {
                sh "docker build --build-arg ENVIRONMENT=sandbox --tag ${imageName}:develop ."
                sh " docker tag ${imageName}:develop ${registry}/${imageName}:develop"
            }
        if (env.BRANCH_NAME == 'preprod') {
                sh "docker build --build-arg ENVIRONMENT=staging --tag ${imageName}:preprod ."
                sh " docker tag ${imageName}:preprod ${registry}/${imageName}:preprod"
            }

        if (env.BRANCH_NAME == 'master') {
                sh "docker build --build-arg ENVIRONMENT=production --tag ${imageName}:master ."
                sh " docker tag ${imageName}:master ${registry}/${imageName}:master"                       
            }  
    }
    
        
    stage('Push'){
         if (env.BRANCH_NAME == 'develop') {
                sh "docker push ${registry}/${imageName}:develop"
            }
         if (env.BRANCH_NAME == 'preprod') {
                sh "docker push ${registry}/${imageName}:preprod"
            }
         if (env.BRANCH_NAME == 'master') {
                sh "docker push ${registry}/${imageName}:master"
            }
    }
    
    
    
}