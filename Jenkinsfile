pipeline {
    agent any

    environment {
        // Change to your server IP where Apache/Nginx is hosting firmware
        SERVER_IP = "192.168.1.50"
        FIRMWARE_PATH = "/var/www/html/firmware"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/yourusername/esp32-ota-project.git'
            }
        }

        stage('Build Firmware') {
            steps {
                sh 'platformio run'
            }
        }

        stage('Copy Firmware to HTTP Server') {
            steps {
                sh 'cp .pio/build/esp32dev/firmware.bin ${FIRMWARE_PATH}/firmware.bin'
            }
        }

        stage('Publish MQTT Update Message') {
            steps {
                sh '''
                mosquitto_pub -h localhost -t esp32/update -m '{
                  "version": "1.2",
                  "url": "http://${SERVER_IP}/firmware/firmware.bin"
                }'
                '''
            }
        }
    }

    post {
        success {
            echo '✅ OTA firmware build and publish completed successfully!'
        }
        failure {
            echo '❌ OTA pipeline failed. Check logs.'
        }
    }
}
