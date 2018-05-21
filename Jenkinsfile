
pipeline {
    agent any
    tools { 
        maven 'Maven 3.3.9'
    }
    parameters {
        string(name: 'CSF_ENVIRONMENT_URL', defaultValue: 'https://bootstrap.saas.stratio.com/service/marathon/v2/apps/', description: 'Environment URL to deploy artifacts once successful build')
        password(name: 'SSO_CLIENT_ID', defaultValue: 'adminrouter_paas-master-1.node.paas.labs.stratio.com', description: 'Client ID to be used as Oauth2 client in authentication')
        string(name: 'SSO_REDIRECT_URL', defaultValue: 'https://bootstrap.saas.stratio.com/service/marathon/v2/apps//acs/api/v1/auth/login', description: 'DC/OS API Login URL to be redirected after successful login in platform')
	string(name: 'DOCKER_REG_URL', defaultValue: '', description: 'Docker registry URL')
        string(name: 'DOCKER_REG_USERNAME', defaultValue: 'csfstratio', description: 'Docker registry username')
        password(name: 'DOCKER_REG_PASSWORD', defaultValue: 'Azrael_01', 'description': 'Docker registry password')
        string(name: 'MARATHON_API_URL', defaultValue: 'https://bootstrap.saas.stratio.com/service/marathon/v2/apps/', description: 'Marathon API URL to deploy docker image')
	string(name: 'GITHUB_MICRO_BASE_URL', defaultValue: 'https://github.com/ahernandez-stratio', description: 'GitHub microservices base URL')
    }

    stages {

       stage('scm') {
           steps {
               checkout([$class: 'GitSCM', branches: [[name: '*/develop']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'githubuser', url: "${params.GITHUB_MICRO_BASE_URL}/${env.JOB_NAME}.git"]]])
           }
       }

       stage('readPom') {
           steps {
               script {
                    pom = readMavenPom file: 'pom.xml'
	                env.POM_VERSION = pom.version
               }
           }
       }
       
       stage ('Build & SonarQube analysis') {
            steps {
                sh 'mvn clean deploy sonar:sonar --settings /opt/settings.xml'
            }
        
       }

        stage('Deploy') {
            steps {
                sh '/opt/dcos_deploy.sh'
            }
        }
    }
}
