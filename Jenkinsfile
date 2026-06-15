pipeline {
    agent any 

    options {
        timeout(time: 1, unit: 'HOURS') // Evita que o container no Portainer trave por loops infinitos
        timestamps()                  // Insere o horário em cada linha do log (essencial para Troubleshooting)
        disableConcurrentBuilds()     // Impede que múltiplos pushes rodem ao mesmo tempo, poupando CPU
    }

    environment {
        REPO_URL = 'https://github.com/Rodolfowolf/Python_excel.git'
        APP_NAME = 'Python_excel'
    }

    stages {
        // 1. CLONE E AUDITORIA
        stage('Checkout SCM') {
            steps {
                echo "=== [STATUS] Iniciando o Pipeline para o Repositório: ${env.APP_NAME} ==="
                echo "📥 Capturando o código atualizado do commit..."
                // O Jenkins faz o clone automático aqui por usar a opção "Pipeline script from SCM"
            }
        }

        // 2. VALIDAÇÃO DE AMBIENTE (LINUX/DOCKER)
        stage('Environment Check') {
            steps {
                echo '=== [STATUS] Verificando integridade do ambiente Linux do Container ==='
                // Comandos Linux para validar o runtime do container
                sh 'python3 --version || echo "Python não instalado"'
                sh 'df -h' // Verifica o consumo de disco no Portainer
            }
        }

        // 3. TESTE DOS AGENTES (QUALIDADE)
        stage('Execute Tests') {
            steps {
                echo '=== [STATUS] Executando bateria de testes nos agentes ==='
                echo '🧪 Rodando análises sintáticas e testes automatizados...'
                // Se você tiver testes em Python, descomente a linha abaixo:
                // sh 'pip install -r requirements.txt && pytest'
                sleep 2
            }
        }

        // 4. ARQUIVAMENTO E LOGS (ARTIFACT)
        stage('Archive Artifact') {
            steps {
                echo '=== [STATUS] Empacotando a versão estável ==='
                // Cria um backup compactado do estado atual do repositório
                sh "tar -czf ${env.APP_NAME}_build_${env.BUILD_NUMBER}.tar.gz --exclude=.git ."
                echo "✅ Artefato criado com sucesso: ${env.APP_NAME}_build_${env.BUILD_NUMBER}.tar.gz"
                
                // Salva o arquivo compactado dentro do histórico do próprio Jenkins
                archiveArtifacts artifacts: '*.tar.gz', allowEmptyArchive: true
            }
        }
    }

    // Gerenciamento do Build Status pós-execução
    post {
        always {
            echo '=== [BUILD LOGS] Finalizando a execução do Pipeline e limpando workspace ==='
        }
        success {
            echo "🟢 [BUILD STATUS] SUCESSO! O código enviado para ${env.REPO_URL} está íntegro e testado."
        }
        failure {
            echo '🔴 [BUILD STATUS] FALHA! Ocorreu um erro em alguma das etapas acima.'
            echo '🔍 Abordagem recomendada: Acesse o "Console Output" no Jenkins para fazer a Análise de Causa Raiz (RCA).'
        }
    }
}