def imageName = 'paulappz/quote-service'

node('workers'){
    stage('Checkout'){
        checkout scm
    }
    
    stage('Unit Tests'){
        def imageTest= docker.build("${imageName}-test", "-f Dockerfile.test .")
        imageTest.inside{
        //    sh "python test_main.py"
        }
    }
}