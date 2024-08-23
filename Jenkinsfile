pipeline {
    agent { label 'ubuntu-vm' }
    
    stages {
	
        stage('Update System') {
            steps {
                sh 'sudo apt update'
            }
        }
        
        stage('Install Apache2') {
            steps {
                sh 'sudo apt install -y apache2'
            }
        }
        
        stage('Start Apache2 Service') {
            steps {
                sh 'sudo systemctl start apache2'
                sh 'sudo systemctl enable apache2'
            }
        }
        
        stage('Check Apache2 Status') {
            steps {
                sh 'sudo systemctl status apache2'
            }
        }
        
        
        stage('Access Apache Server') {
            steps {
                script {
                    // Access the Apache server to generate logs
                    def url = 'http://localhost'
                    echo "Accessing Apache server at ${url}..."
                    sh "curl -I ${url} || true"  // Fetch the headers to generate a log entry
                }
            }
        }
        
        
        stage('Generate 4xx and 5xx Errors') {
            steps {
                script {
                    // Access a non-existent page to generate a 404 error
                    echo "Generating 404 Not Found error..."
                    sh "curl -I http://localhost/nonexistentpage || true"

                    // Access a forbidden directory to generate a 403 error (if configured)
                    echo "Generating 403 Forbidden error..."
                    sh "curl -I http://localhost/root || true"

                    // Attempt to generate a 500 Internal Server Error
                    // Note: Generating a 500 error intentionally requires specific server-side conditions or misconfigurations
                    echo "Generating 500 Internal Server Error..."
                    sh "curl -I http://localhost/cgi-bin/faulty-script || true"
                }
            }
        }
        
        stage('Check for 4xx and 5xx Errors in Logs') {
            steps {
                script {
                    def errorOutput = sh(
                        script: "sudo grep -E 'HTTP/[0-9\\.]+\" [45][0-9]{2}' /var/log/apache2/access.log || true",
                        returnStdout: true
                    ).trim()
                    
                    if (errorOutput) {
                        echo '4xx or 5xx errors found in Apache logs!'
                        echo errorOutput
                    } else {
                        echo 'No 4xx or 5xx errors found in Apache logs.'
                    }
                }
            }
        }
    }
    
    post {
        always {
            echo 'Cleaning up workspace...'
            cleanWs()
        }
    }
}
