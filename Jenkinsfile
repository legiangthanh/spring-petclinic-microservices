pipeline {
    agent any
    environment {
        SONAR_PROJECT_KEY = 'spring-petclinic-microservices'
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            steps {
                sh './mvnw clean install -DskipTests'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh "./mvnw sonar:sonar -Dsonar.projectKey=${SONAR_PROJECT_KEY}"
                }
            }
        }

        stage('Snyk Scan') {
            steps {
                // 'snyk-token-id' is the ID of your Snyk API token credential
                snykSecurity(
                    snykInstallation: 'Snyk', 
                    snykTokenId: 'SNYK_TOKEN_ID',
                    targetFile: 'pom.xml'
                )
            }
        }
        

        /*stage('OWASP ZAP Baseline Scan') {
            steps {
                sh '''
                    echo "=== Before scan ==="
                    ls -la
                    
                    docker run --rm --user 0 --network sourcecode_default \
                        -v $PWD:/zap/wrk:rw -t zaproxy/zap-stable zap-baseline.py \
                        -t http://api-gateway:8080 \
                        -r zap-report.html \
                        -I
                    
                    echo "=== After scan ==="
                    ls -la
                    
                    if [ -f zap-report.html ]; then
                        echo "Report found!"
                        cat zap-report.html | head -20
                    else
                        echo "Report NOT found!"
                    fi
                '''
                
                archiveArtifacts artifacts: 'zap-report.html', allowEmptyArchive: true
            }
        }*/
        
    }
}