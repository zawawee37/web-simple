pipeline {
    // กำหนด agent ที่จะใช้รัน Pipeline
    // agent any หมายถึงใช้ agent ใดก็ได้ที่ว่างอยู่
    agent any

    // กำหนดเครื่องมือ (Tools) ที่จำเป็นสำหรับ Pipeline นี้
    // ถ้า Jenkins Agent (คอนเทนเนอร์ Jenkins) ถูกตั้งค่าให้มี git และ docker อยู่ใน PATH อยู่แล้ว
    // ส่วนนี้อาจไม่จำเป็นต้องระบุ หรือระบุเพื่อความชัดเจน
    // tools {
    //     git 'Default' // 'Default' คือชื่อ Git configuration ใน Global Tool Configuration
    // }

    // กำหนด Stages ต่างๆ ของ Pipeline
    stages {
        // Stage สำหรับโคลนโค้ดจาก GitHub Repository
        stage('Clone Repository') {
            steps {
                // ใช้คำสั่ง git เพื่อโคลน repository
                // branch: 'master' คือชื่อ branch ที่ต้องการโคลน
                // url: '...' คือ URL ของ GitHub Repository
                // ถ้าเป็น private repository, ต้องเพิ่ม credentialsId: 'your-github-credentials-id'
                git branch: 'master', url: 'https://github.com/zawawee37/web-simple.git'
            }
        }

        // Stage สำหรับสร้าง Docker Image
        stage('Build Docker Image') {
            steps {
                script {
                    // ใช้ Docker Pipeline DSL เพื่อสร้าง Docker Image
                    // "my-web-cicd:${env.BUILD_NUMBER}" คือชื่อ Image และ Tag (ใช้หมายเลข Build ของ Jenkins)
                    // "." คือ build context ซึ่งหมายถึง Dockerfile อยู่ในไดเรกทอรีปัจจุบันของ workspace
                    def appImage = docker.build("my-web-cicd:${env.BUILD_NUMBER}", ".")

                    // (ทางเลือก) คุณสามารถเพิ่มการ push image ไปยัง Docker Hub หรือ Registry อื่นๆ ได้ที่นี่
                    // โดยต้องตั้งค่า Docker Registry ใน Jenkins Credentials ก่อน
                    // appImage.push()

                    // (ทางเลือก) สร้าง Tag 'latest' สำหรับ Image ล่าสุดที่ Build สำเร็จ
                    // appImage.tag('my-web-cicd:latest')
                    // appImage.push('latest')
                }
            }
        }

        // Stage สำหรับรัน Docker Container
        stage('Run Container') {
            steps {
                script {
                    // ใช้คำสั่ง shell เพื่อจัดการ container เก่า
                    // docker rm -f my-web: ลบ container เก่าชื่อ my-web ถ้ามี
                    // || true: ทำให้ pipeline ไม่ fail ถ้า container my-web ไม่มีอยู่
                    sh 'docker rm -f my-web || true'

                    // ใช้ Docker Pipeline DSL หรือคำสั่ง shell เพื่อรัน container ใหม่
                    // docker run -d --name my-web -p 9090:80 my-web-cicd
                    // -d: รันแบบ detached (background)
                    // --name my-web: กำหนดชื่อ container
                    // -p 9090:80: แมปพอร์ต 9090 ของ Host ไปยังพอร์ต 80 ของ container
                    // my-web-cicd: ชื่อ Image ที่จะใช้รัน (จะใช้ Tag ล่าสุดโดยอัตโนมัติหากไม่ได้ระบุ)
                    sh "docker run -d --name my-web -p 9090:80 my-web-cicd:${env.BUILD_NUMBER}"
                    // หรือจะใช้ "my-web-cicd:latest" ถ้ามีการ tag latest
                }
            }
        }
    }

    // ส่วน Post-build actions (ไม่บังคับ)
    // สามารถใช้ส่งแจ้งเตือนหรือทำความสะอาดหลังจาก Pipeline เสร็จสิ้น
    post {
        always {
            echo "Pipeline finished. Check http://localhost:9090 for the application."
            // cleanWs() // ทำความสะอาด workspace หลังจาก build
        }
        success {
            echo "Pipeline finished successfully!"
        }
        failure {
            echo "Pipeline failed! Check logs for details."
        }
    }
}
