pipeline {
    agent any

    environment {
        PAIS = 'CL'
        DB_SERVICE_PROVIDER = 'GCP - Cloud SQL'
        DB_ENGINE = 'SQL Server'
        DB_BACKUP_ENABLED = 'true'
        DB_TIME_ZONE = 'America/Santiago'
        DB_RESOURCE_LABELS = 'test'
        DB_TAGS = 'test'
        DB_PLATFORM_USER = 'equipo_plataforma'
        DB_PLATFORM_PASS = 'password'
        DB_USER_ADMIN = 'postgres'
        DB_PASSWORD_ADMIN = 'password'

        // URL Jira
        JIRA_API_URL = "https://bancoripley1.atlassian.net/rest/api/3/issue/"
    }

    options {
        timeout(time: 2, unit: 'HOURS')
        timestamps()
        buildDiscarder(logRotator(numToKeepStr: '15'))
        disableConcurrentBuilds()
    }

    parameters {
        // ===========================
        // GCP Configuración
        // ===========================
        string(name: 'PROYECT_ID', defaultValue: '', description: 'Ingrese el ID del proyecto')
        string(name: 'REGION', defaultValue: '', description: 'Ingrese la región')
        string(name: 'ZONE', defaultValue: '', description: 'Ingrese la zona')
        choice(name: 'ENVIRONMENT', choices: ['1-Desarrollo', '2-Pre productivo (PP)', '3-Producción'], description: 'Seleccionar ambiente')

        // ===========================
        // DB Instance
        // ===========================
        string(name: 'DB_INSTANCE_NAME', defaultValue: '', description: 'Ingrese el nombre de la instancia')
        string(name: 'DB_INSTANCE_ID', defaultValue: '', description: 'Ingrese el ID de la instancia')
        choice(name: 'DB_AVAILABILITY_TYPE', choices: ['Regional', 'Zonal'], description: 'Seleccione tipo de disponibilidad')
        choice(name: 'DB_VERSION', choices: ['SQL Server 2022', 'SQL Server 2019', 'SQL Server 2017'], description: 'Seleccione versión de base de datos')
        string(name: 'DB_TIME_ZONE', defaultValue: '', description: 'Ingrese zona horaria')

        // ===========================
        // Máquina
        // ===========================
        choice(name: 'MACHINE_TYPE', choices: ['db-custom-4-16384', 'db-custom-8-32768'], description: 'Seleccione el tipo de máquina')
        string(name: 'DB_MAX_CONNECTIONS', defaultValue: '', description: 'Ingrese número máximo de conexiones a base de datos')
        string(name: 'DB_STORAGE_SIZE', defaultValue: '', description: 'Ingrese tamaño de almacenamiento')
        choice(name: 'DB_STORAGE_AUTO_RESIZE', choices: ['true', 'false'], description: 'Habilitar aumento automático de almacenamiento')
        choice(name: 'DB_STORAGE_TYPE', choices: ['SSD', 'HDD'], description: 'Seleccione tipo de almacenamiento')
        string(name: 'DB_USERNAME', defaultValue: 'sa', description: 'Ingrese nombre de usuario')
        string(name: 'DB_PASSWORD', defaultValue: 'password', description: 'Ingrese contraseña')

        // ===========================
        // Redes
        // ===========================
        string(name: 'DB_VPC_NETWORK', defaultValue: '', description: 'Ingrese red VPC')
        string(name: 'DB_SUBNET', defaultValue: '', description: 'Ingrese subred de base de datos')
        choice(name: 'DB_PUBLIC_ACCESS_ENABLED', choices: ['false', 'true'], description: 'Habilitar acceso público')
        choice(name: 'DB_PRIVATE_IP_ENABLED', choices: ['true', 'false'], description: 'Habilitar IP privada')
        choice(name: 'DB_IP_RANGE_ALLOWED', choices: ['false', 'true'], description: 'Rangos permitidos (CIDR)')
        choice(name: 'DB_SSL_ENABLED', choices: ['true', 'false'], description: 'Habilitar SSL')

        // ===========================
        // Seguridad y Operación
        // ===========================
        string(name: 'DB_BACKUP_START_TIME', defaultValue: '', description: 'Ingrese comienzo de backup')
        string(name: 'DB_BACKUP_RETENTION_DAYS', defaultValue: '', description: 'Ingrese cantidad de días de retención de backup')
        string(name: 'DB_MAINTENANCE_WINDOW_DAY', defaultValue: '', description: 'Ingrese día de mantención')
        string(name: 'DB_MAINTENANCE_WINDOW_HOUR', defaultValue: '', description: 'Ingrese hora de mantención')
        choice(name: 'DB_MONITORING_ENABLED', choices: ['true', 'false'], description: 'Habilitar monitoreo')
        choice(name: 'DB_AUDIT_LOGS_ENABLED', choices: ['true', 'false'], description: 'Habilitar logs de auditoría')
        choice(name: 'CREDENTIAL_FILE', choices: ['false', 'true'], description: '¿Usar archivo de credenciales?')
        string(name: 'DB_IAM_ROLE', defaultValue: '', description: 'Ingrese roles IAM')
        choice(name: 'DB_DELETION_PROTECTION', choices: ['true', 'false'], description: 'Habilitar protección contra eliminación')
        choice(name: 'CHECK_DELETE', choices: ['true', 'false'], description: 'Seleccione eliminación automática')
        choice(name: 'DB_ENCRYPTION_ENABLED', choices: ['true', 'false'], description: 'Habilitar encriptación')

        // ===========================
        // Replica / Failover
        // ===========================
        choice(name: 'DB_FAILOVER_REPLICA_ENABLED', choices: ['false', 'true'], description: 'Habilitar recuperación automática en caso de falla de servidor principal')
        choice(name: 'DB_READ_REPLICA_ENABLED', choices: ['false', 'true'], description: 'Habilitar creación de réplica de la base de datos')

        // ===========================
        // Licenciamiento SQL Server
        // ===========================
        choice(name: 'DB_SQL_SERVER_LICENSE_MODEL', choices: ['PAYG', 'BRING_YOUR_OWN_LICENSE'], description: 'Seleccione el modelo de licencia para SQL Server')

        // ===========================
        // Caché
        // ===========================
        choice(name: 'ENABLE_CACHE', choices: ['false', 'true'], description: 'Habilitar cache de datos en la base de datos')

        // ===========================
        // Ticket Jira
        // ===========================
        string(name: 'TICKET_JIRA', defaultValue: 'AJI-83', description: 'Ingresar ticket a Jira')
    }

    stages {
        stage('Imprimir variables') {
            steps {
                script {
                    echo '======== VARIABLES PRINCIPALES ========'
                    def variables = [
                        PROYECT_ID: params.PROYECT_ID,
                        REGION: params.REGION,
                        ZONE: params.ZONE,
                        ENVIRONMENT: params.ENVIRONMENT,
                        DB_INSTANCE_NAME: params.DB_INSTANCE_NAME,
                        DB_INSTANCE_ID: params.DB_INSTANCE_ID,
                        DB_AVAILABILITY_TYPE: params.DB_AVAILABILITY_TYPE,
                        DB_VERSION: params.DB_VERSION
                        // ...puedes seguir agregando las demás como en el ejemplo anterior
                    ]
                    variables.each { k, v -> echo "${k}: ${v}" }
                }
            }
        }

        // --- BLOQUES TERRAFORM COMENTADOS ---
        /*
        stage('Terraform Init & Plan') {
            steps {
                dir('terraform') {
                    script {
                        withCredentials([file(credentialsId: 'gcp-sa-platform', variable: 'GOOGLE_CREDENTIALS')]) {
                            bat """
                                set GOOGLE_APPLICATION_CREDENTIALS=%GOOGLE_CREDENTIALS%
                                terraform init
                                terraform plan -out=tfplan
                            """
                        }
                    }
                }
            }
        }

        stage('Terraform Apply') {
            steps {
                dir('terraform') {
                    script {
                        withCredentials([file(credentialsId: 'gcp-sa-platform', variable: 'GOOGLE_CREDENTIALS')]) {
                            bat """
                                set GOOGLE_APPLICATION_CREDENTIALS=%GOOGLE_CREDENTIALS%
                                terraform apply tfplan
                            """
                        }
                    }
                }
            }
        }

        stage('Terraform Destroy') {
            when {
                expression { return params.ENVIRONMENT == '3-Producción' }
            }
            steps {
                dir('terraform') {
                    script {
                        withCredentials([file(credentialsId: 'gcp-sa-platform', variable: 'GOOGLE_CREDENTIALS')]) {
                            bat """
                                set GOOGLE_APPLICATION_CREDENTIALS=%GOOGLE_CREDENTIALS%
                                terraform destroy -auto-approve
                            """
                        }
                    }
                }
            }
        }
        */

        // Bloque 1: Consultar estado del ticket Jira
        stage('Post-Jira Status') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'JIRA_TOKEN', usernameVariable: 'JIRA_USER', passwordVariable: 'JIRA_API_TOKEN')]) {
                        def auth = java.util.Base64.encoder.encodeToString("${JIRA_USER}:${JIRA_API_TOKEN}".getBytes("UTF-8"))
                        def response = bat(
                            script: """
                                curl -s -X GET "${JIRA_API_URL}${params.TICKET_JIRA}" ^
                                -H "Authorization: Basic ${auth}" ^
                                -H "Accept: application/json"
                            """,
                            returnStdout: true
                        ).trim()
                        def json = new groovy.json.JsonSlurper().parseText(response)
                        def estado = json.fields.status.name
                        echo "Estado actual del ticket ${params.TICKET_JIRA}: ${estado}"
                    }
                }
            }
        }

        // Bloque 2: Comentar en Jira
        stage('Post-Coment-jira') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'JIRA_TOKEN', usernameVariable: 'JIRA_USER', passwordVariable: 'JIRA_API_TOKEN')]) {
                        def auth = java.util.Base64.encoder.encodeToString("${JIRA_USER}:${JIRA_API_TOKEN}".getBytes("UTF-8"))
                        def comentario = "Este ticket fue comentado por Scarlet SC"

                        def response = bat(
                            script: """
                                curl -s -X POST "${JIRA_API_URL}${params.TICKET_JIRA}/comment" ^
                                -H "Authorization: Basic ${auth}" ^
                                -H "Content-Type: application/json" ^
                                -d "{\\"body\\": {\\"type\\": \\"doc\\", \\"version\\": 1, \\"content\\": [{\\"type\\": \\"paragraph\\", \\"content\\": [{\\"type\\": \\"text\\", \\"text\\": \\"${comentario}\\"}]}]}}"
                            """,
                            returnStdout: true
                        ).trim()

                        echo "Comentario enviado al ticket ${params.TICKET_JIRA}: ${response}"
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline ejecutado correctamente.'
        }
        failure {
            echo 'Error al ejecutar el pipeline.'
        }
    }
}
