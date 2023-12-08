working code below
====================================================

pipeline {
    agent any
    tools {
        maven "MAVEN_H"
    }
    stages {
        stage('MavenBuild') {
            steps {
				checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'Ascend_team3', url: 'https://github.com/walmart-2023-sep-ascend/TeamPDP03.git']])
                // Run Maven build commands
                bat 'mvn clean install'
            }
        }
    }
}

====================================================================

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

ascend  git token -- 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

==========================================================

pipeline {
    agent any

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven 'M2_HOME'
    }

    stages {
        stage('MavenBuild') {
            steps {
                // Get code from a GitHub repository
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: '68ea2dce-2efa-438a-bcf3-7cab31dcbd7a', url: 'https://github.com/walmart-2023-sep-ascend/Team5-Apply-PromoCode.git']])
                // Set JAVA_HOME , M2_HOME, mvn clean install
                sh '''export JAVA_HOME=/opt/homebrew/opt/openjdk@17/libexec/openjdk.jdk/Contents/Home
                        export M2_HOME=/opt/homebrew/Cellar/maven/3.9.5/libexec
                        export PATH=$PATH:$M2_HOME/bin
                        mvn clean install'''
            }
        }
        stage('DockerBuild') {
            steps {
                script {
                    //sh 'docker build -f Dockerfile -t ksvsbdocker/team5-apply-promocode_`date +%Y-%m-%d_%H-%M-%S`:v1 .'
                    sh '''docker build -f Dockerfile -t ksvsbdocker/team5-apply-promocode_`date +%Y-%m-%d_%H-%M-%S`:v1 .
                        docker tag `docker images | grep \'ksvsbdocker/team5-apply-promocode\' | head -1 | awk \'{print $1":"$2;}\'` ksvsbdocker/team5-offers-be:latest'''
                }
            }
        }
        stage('DockerPush') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'DockerHubPassword', variable: 'DockerHUBPassword')]) {
                        sh '''docker login -u ksvsbdocker -p ${DockerHUBPassword}
                                docker push `docker images | grep \'ksvsbdocker/team5-apply-promocode_\' | head -1 | awk \'{print $1":"$2;}\'`
                                docker push ksvsbdocker/team5-offers-be:latest'''
                    }
                    
                }
            }
        }
    }
}

===================
pipeline {
    agent any
    tools {
        maven "MAVEN_H"
    }
    stages {
        stage('MavenBuild') {
            steps {
				checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'Ascend_team3', url: 'https://github.com/walmart-2023-sep-ascend/TeamPDP03.git']])
                // Run Maven build commands
                bat 'mvn clean install'
            }
        }
        stage('dockerBuild') {
            steps {
                script {
                    sh 'docker build -t maruthi448/ProductApplication-0.0.1-SNAPSHOT .'
                }
                
            }
        }
    }
}
