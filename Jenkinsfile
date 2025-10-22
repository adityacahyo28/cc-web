pipeline {
    agent any

    environment {
        APP_IMAGE = 'laravelweb'
        APP_CONTAINER = 'laravelcoba'
      
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo "🔄 Checkout source code dari GitHub..."
                git branch: 'main', url: 'https://github.com/adityacahyo28/cc-web.git'
            }
        }

        stage('Build Docker Images') {
            steps {
                echo "🏗️ Build Docker image Laravel dari Dockerfile..."
                bat 'docker-compose build'
            }
        }

        stage('Run Docker Containers') {
            steps {
                echo "🚀 Jalankan ulang container Laravel & MySQL..."
                bat '''
                echo ==== HENTIKAN CONTAINER LAMA ====
                docker stop %APP_CONTAINER% || echo "Container Laravel belum jalan"
                docker rm %APP_CONTAINER% || echo "Container Laravel sudah dihapus"
                docker stop %MYSQL_CONTAINER% || echo "Container MySQL belum jalan"
                docker rm %MYSQL_CONTAINER% || echo "Container MySQL sudah dihapus"

                echo ==== JALANKAN ULANG DENGAN DOCKER COMPOSE ====
                docker-compose down || exit 0
                docker-compose up -d

                echo ==== CEK CONTAINER YANG AKTIF ====
                docker ps
                '''
            }
        }

        stage('Verify Container Running') {
            steps {
                echo "🔍 Verifikasi Laravel container berjalan dengan benar..."
                bat '''
                echo ==== TUNGGU 20 DETIK SUPAYA CONTAINER SIAP ====
                ping 127.0.0.1 -n 20 >nul

                echo ==== CEK KONEKSI KE LARAVEL ====
                curl -I http://127.0.0.1:8000 || echo "⚠ Gagal akses Laravel di port 8000"
                
                echo.
                echo ==== ISI HALAMAN (HARUSNYA MUNCUL HALAMAN LARAVEL) ====
                curl http://127.0.0.1:8000 || echo "⚠ Gagal ambil isi halaman"
                echo ===============================
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Laravel berhasil dijalankan via Docker Compose di port 8000!'
        }
        failure {
            echo '❌ Build gagal, cek log Jenkins console output.'
        }
    }
}
