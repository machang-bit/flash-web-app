pipeline {
    agent any
    
    environment {
        // =========已经替换成你本机Python路径=========
        PYTHON_PATH = 'D:\\Python315\\python.exe'
    }
    
    stages {
        stage('Verify Python Path') {
            steps {
                bat """
                    @echo off
                    echo "=== 验证Python版本 ==="
                    ${PYTHON_PATH} --version
                    echo "Python路径验证通过！"
                """
            }
        }
        stage('Checkout') {
            steps {
                // 拉取你的github仓库代码
                git url: 'https://github.com/machang-bit/flash-web-app.git', branch: 'main'
            }
        }
        stage('Fix pip') {
            steps {
                bat """
                    @echo off
                    echo "=== 升级pip ==="
                    ${PYTHON_PATH} -m ensurepip --upgrade  
                    ${PYTHON_PATH} -m pip --version       
                """
            }
        }
        stage('Install Dependencies') {
            steps {
                bat """
                    @echo off
                    ${PYTHON_PATH} -m pip install --upgrade pip
                    ${PYTHON_PATH} -m pip install -r requirements.txt
                """
            }
        }
        stage('Lint 代码静态检查') {
            steps {
                bat "${PYTHON_PATH} -m pip install flake8 && ${PYTHON_PATH} -m flake8 app.py tests/"
            }
        }
        stage('Test 单元测试+覆盖率') {
            steps {
                bat """
                    ${PYTHON_PATH} -m pip install pytest pytest-cov
                    ${PYTHON_PATH} -m pytest --cov=app tests/ --cov-report=html
                """
            }
            post {
                always {
                    publishHTML(target: [
                        allowMissing: false,
                        alwaysLinkToLastBuild: false,
                        keepAll: true,
                        reportDir: 'htmlcov',
                        reportFiles: 'index.html',
                        reportName: 'Coverage Report'
                    ])
                }
            }
        }
        stage('Build 打包exe') {
            steps {
                bat """
                    ${PYTHON_PATH} -m pip install pyinstaller
                    ${PYTHON_PATH} -m PyInstaller --onefile app.py
                """
            }
        }
        stage('Deploy 启动Flask') {
            steps {
                echo 'Deploying application...'
                bat "start ${PYTHON_PATH} app.py"
            }
        }
    }
    post {
        success { echo '✅ CI/CD流水线全部执行成功!' }
        failure { echo '❌ CI/CD流水线执行失败!' }
    }
}
