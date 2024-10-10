pipeline {
    agent any

    environment {
        PROMETHEUS_VERSION = "v2.41.0"
        GRAFANA_VERSION = "latest"
    }

    stages {
        stage('Prepare Environment') {
            steps {
                script {
                    // Ensure the directories exist for Prometheus config and Grafana storage
                    sh "mkdir -p /path/to/prometheus"
                    sh "mkdir -p /path/to/grafana-storage"
                    
                    // Write a sample Prometheus configuration file
                    writeFile file: '/path/to/prometheus/prometheus.yml', text: '''global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']
  - job_name: 'node'
    static_configs:
      - targets: ['localhost:9100']
'''
                }
            }
        }
        
        stage('Pull Docker Images') {
            steps {
                script {
                    // Pull Prometheus Docker image
                    sh "docker pull prom/prometheus:${PROMETHEUS_VERSION}"
                    
                    // Pull Grafana Docker image
                    sh "docker pull grafana/grafana:${GRAFANA_VERSION}"
                }
            }
        }
        
        stage('Run Prometheus Container') {
            steps {
                script {
                    // Run Prometheus container with the custom configuration
                    sh """
                        docker run -d \
                        --name prometheus \
                        -p 9090:9090 \
                        -v /path/to/prometheus/prometheus.yml:/etc/prometheus/prometheus.yml \
                        prom/prometheus:${PROMETHEUS_VERSION}
                    """
                }
            }
        }
        
        stage('Run Grafana Container') {
            steps {
                script {
                    // Run Grafana container with a persistent data volume
                    sh """
                        docker run -d \
                        --name grafana \
                        -p 3000:3000 \
                        -v /path/to/grafana-storage:/var/lib/grafana \
                        grafana/grafana:${GRAFANA_VERSION}
                    """
                }
            }
        }

        stage('Post Deployment Info') {
            steps {
                echo "Prometheus is available at: http://<Jenkins-server-IP>:9090"
                echo "Grafana is available at: http://<Jenkins-server-IP>:3000"
            }
        }
    }
    
    post {
        always {
            echo "Pipeline execution complete."
        }
    }
}