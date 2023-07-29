def imageName = 'paulappz/quote-service'
def registry = '570942461061.dkr.ecr.eu-west-2.amazonaws.com' 
def region = 'eu-west-2'
def accounts = [master:'production', preprod:'staging', develop:'sandbox']

node('workers'){
try {
    stage('Checkout'){
        checkout scm
        notifySlack('STARTED')
    }
    
    stage('Unit Tests'){
        // def imageTest= docker.build("${imageName}-test", "-f Dockerfile.test .")
        //imageTest.inside{
        //    sh "python test_main.py"
        //}
    }
    
    stage('Build'){
                    docker.build(imageName)
            
        //,
    
        //    sh "aws ecr get-login-password --region ${region} | docker login --username AWS --password-stdin ${registry}/${imageName}"
        //    if (${env.BUILD_NUMBER} == 'develop' || ${env.BUILD_NUMBER} == 'preprod' || env.BRANCH_NAME == 'master'  ) {
        //    sh "docker build --build-arg ENVIRONMENT=${accounts[env.BRANCH_NAME]} --tag ${imageName} ."
        //     }
    }
    
        
    stage('Push'){
     if (env.BRANCH_NAME == 'develop' || env.BRANCH_NAME == 'preprod' || env.BRANCH_NAME == 'master'  ) {
                sh " docker tag ${imageName}:latest ${registry}/${imageName}:env.BRANCH_NAME"
                sh "docker push ${registry}/${imageName}:env.BRANCH_NAME"
                }
    }
    
    stage('Analyze'){
     if (env.BRANCH_NAME == 'develop' || env.BRANCH_NAME == 'preprod' || env.BRANCH_NAME == 'master'  ) {
        def scannedImage = "${registry}/${imageName}:env.BRANCH_NAME"
         writeFile file: 'images', text: scannedImage
             anchore name: 'images'
     }
        }
    
    stage('Deploy'){
        if(env.BRANCH_NAME == 'develop' || env.BRANCH_NAME == 'preprod'){
                build job: "quote-microservice-deployments/${env.BRANCH_NAME}"
        }
        if(env.BRANCH_NAME == 'master'){
                timeout(time: 2, unit: "HOURS") {
                input message: "Approve Deploy?", ok: "Yes"
        }
                build job: "quote-microservice-deployments/master"
            }
        }
    }
        catch(e){
        currentBuild.result = 'FAILED'
        throw e
    } finally {
        notifySlack(currentBuild.result)
    }
}

def notifySlack(String buildStatus){
    buildStatus =  buildStatus ?: 'SUCCESSFUL'
    def colorCode = '#FF0000'
    def subject = "Name: '${env.JOB_NAME}'\nStatus: ${buildStatus}\nBuild ID: ${env.BUILD_NUMBER}"
    def summary = "${subject}\nMessage: ${commitMessage()}\nAuthor: ${commitAuthor()}\nURL: ${env.BUILD_URL}"

    if (buildStatus == 'STARTED') {
        colorCode = '#546e7a'
    } else if (buildStatus == 'SUCCESSFUL') {
        colorCode = '#2e7d32'
    } else {
        colorCode = '#c62828c'
    }

    slackSend (color: colorCode, message: summary)
}


def commitAuthor(){
    sh 'git show -s --pretty=%an > .git/commitAuthor'
    def commitAuthor = readFile('.git/commitAuthor').trim()
    sh 'rm .git/commitAuthor'
    commitAuthor
}

def commitID() {
    sh 'git rev-parse HEAD > .git/commitID'
    def commitID = readFile('.git/commitID').trim()
    sh 'rm .git/commitID'
    commitID
}

def commitMessage() {
    sh 'git log --format=%B -n 1 HEAD > .git/commitMessage'
    def commitMessage = readFile('.git/commitMessage').trim()
    sh 'rm .git/commitMessage'
    commitMessage
}