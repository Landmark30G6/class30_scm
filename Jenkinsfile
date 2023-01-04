node{
    def mavenHome = tool name :'maven3.8.6'
    stage('1clonecode'){
        sh "echo 'clone code now '"
        git "https://github.com/elvis-ngeh/maven-web-application"
    }
    stage('2test&build'){
        sh "echo 'Junit test & build '"
        sh "${mavenHome}/bin/mvn clean package"
    }
    stage('3codeQuality'){
        sh"echo 'running code quality analyses now'"
        sh"${mavenHome}/bin/mvn sonar:sonar"
    }
    stage('4uploadartifacts'){
        sh "echo 'uploading artifacts to nexus'"
        sh "${mavenHome}/bin/mvn deploy"
    }
    stage('5UATtest'){
        sh"echo 'testing application'"
        deploy adapters: [tomcat9(credentialsId: 'tomcat-credentials', path: '', url: 'http://10.0.1.148:8080')], contextPath: null, war: 'target/*war'
    }
    stage('6approvalgate'){
        sh"echo 'pls review and approve'"
        timeout(time: 5, unit:'DAYS'){
            input message:'kindly take a look at the application, and approve for final deployment'
        }
        stage('7deploy2prod'){
            sh"echo 'deploying to prod now '"
            deploy adapters: [tomcat9(credentialsId: 'tomcat-credentials', path: '', url: 'http://10.0.1.148:8080')], contextPath: null, war: 'target/*war'
        }
        stage('8notification'){
            emailext body: 'We finally push the application to prod , thanks for your hardwork ', subject: 'Prod deployment ', to: 'elvis.bantar@gmail.com'
        }
    }

}
