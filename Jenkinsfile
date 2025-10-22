pipeline {
    agent any

    environment {
        IMAGE_NAME = "laravelweb"
        CONTAINER_APP = "laravelcoba"
      
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo "🔄 Checkout source code dari repo kamu..."
                git branch: 'main', url: 'https://github.com/adityacahyo28/cc-web.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "🏗️ Build Docker image Laravel dari Dockerfile..."
                bat '''
                echo ==== MENAMBAHKAN SAFE DIRECTORY UNTUK GIT ====
                git config --global --add safe.directory "%cd%"

                echo ==== BUILD DOCKER COMPOSE TANPA CACHE ====
                docker-compose build --no-cache
                '''
            }
        }

        stage('Run Docker Containers') {
            steps {
                echo "🚀 Jalankan ulang container Laravel dan MySQL..."
                bat '''
                echo ==== HENTIKAN CONTAINER LAMA ====
                docker stop %CONTAINER_APP% || echo "%CONTAINER_APP% tidak berjalan"
                docker rm %CONTAINER_APP% || echo "%CONTAINER_APP% sudah dihapus"

                docker stop %CONTAINER_DB% || echo "%CONTAINER_DB% tidak berjalan"
                docker rm %CONTAINER_DB% || echo "%CONTAINER_DB% sudah dihapus"

                echo ==== JALANKAN ULANG DOCKER COMPOSE ====
                docker-compose down || exit 0
                docker-compose up -d

                echo ==== CEK CONTAINER YANG AKTIF ====
                docker ps
                '''
            }
        }

        stage('Verify Laravel Running') {
            steps {
                echo "🔍 Verifikasi Laravel container berjalan di port 8000..."
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
        always {
            echo "🧹 Membersihkan cache Docker..."
            bat 'docker system prune -f'
        }
    }
}
