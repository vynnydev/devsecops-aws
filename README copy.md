Pipeline CI/CD com Jenkins, SonarQube, ECR e ECS 🚀
===================================================

Este projeto implementa um pipeline completo de CI/CD usando Jenkins para uma aplicação Python Flask, com deploy automatizado na AWS utilizando Docker, ECR (Elastic Container Registry) e ECS (Elastic Container Service), Trivy para escaneamento de vulnerabilidades, proxy de segurança com OWASP-ZAP e alertas e monitoramento com Prometheus e Grafana.. Inclui análise de código com SonarQube para manter a qualidade do código.

<img src="/docs/architecture/aws-pipeline-layered-architecture.png" alt="AWS Pipeline Layered Architecture">

📋 Índice
---------

-   [Arquitetura](#arquitetura)
-   [Pré-requisitos](#pr%C3%A9-requisitos)
-   [Estrutura do Projeto](#estrutura-do-projeto)
-   [Configuração da Infraestrutura](#configura%C3%A7%C3%A3o-da-infraestrutura)
-   [Configuração do Jenkins](#configura%C3%A7%C3%A3o-do-jenkins)
-   [Configuração do SonarQube](#configura%C3%A7%C3%A3o-do-sonarqube)
-   [Pipeline CI/CD](#pipeline-cicd)
-   [Deploy da Aplicação](#deploy-da-aplica%C3%A7%C3%A3o)
-   [Monitoramento](#monitoramento)
-   [Troubleshooting](#troubleshooting)
-   [Resultados da Análise de Código](#resultados-da-an%C3%A1lise-de-c%C3%B3digo)
-   [Segurança](#seguran%C3%A7a)
-   [Contribuindo](#contribuindo)
-   [Licença](#licen%C3%A7a)
-   [Autores](#autores)

🏗️ Arquitetura
---------------

### Componentes:

-   **GitHub**: Repositório de código fonte
-   **Jenkins**: Servidor de CI/CD para execução do pipeline
-   **SonarQube**: Ferramenta de análise de qualidade de código
-   **Trivy**: Scanner de vulnerabilidades
-   **OWASP ZAP** - Testes de segurança web
-   **Prometheus** - Métrica
-   **Grafana** - Dashboards
-   **Docker**: Containerização da aplicação
-   **ECR**: Registro de imagens Docker na AWS
-   **ECS/Fargate**: Orquestração de containers sem servidor
-   **ALB**: Load Balancer para distribuição de tráfego

**🚀 Parte em desenvolvimento - DevSecOps completo:**
--------------------------------------------

### **🛡️ Segurança (DevSecOps):**

-   **Trivy** - Scanner de vulnerabilidades
-   **OWASP ZAP** - Testes de segurança web

### **📊 Observabilidade:**

-   **Prometheus** - Métricas
-   **Grafana** - Dashboards

🔧 Pré-requisitos
-----------------

### Ferramentas Necessárias:

-   AWS CLI configurado
-   Terraform >= 1.0
-   Git
-   Docker
-   Conta AWS com permissões adequadas
-   JDK 11 ou superior (para Jenkins)
-   Python 3.9+ (para aplicação de exemplo)

### Conhecimentos Requeridos:

-   Conceitos básicos de CI/CD
-   Docker e containerização
-   AWS Services (EC2, ECS, ECR, VPC)
-   Terraform para IaC
-   Análise de qualidade de código com SonarQube

🚀 Estrutura do Projeto DEVSECOPS-AWS
==================================

```
DEVSECOPS-AWS/
├── docs/                                    # Documentação do projeto
│   ├── architecture/                        # Documentação da arquitetura
│   └── images/                             # Imagens e diagramas
├── pipelines/aws/                          # Pipelines de CI/CD para AWS
│   └── Jenkinsfile                         # Pipeline Jenkins principal
├── sample-app/                             # Aplicação de exemplo
│   ├── .gitignore                          # Arquivos ignorados pelo Git
│   ├── app.py                              # Aplicação Flask Python
│   ├── Dockerfile                          # Imagem Docker da aplicação
│   ├── Jenkinsfile                         # Pipeline Jenkins da aplicação
│   ├── requirements.txt                    # Dependências Python
│   ├── sonar-project.properties            # Configuração do SonarQube
│   └── test_app.py                         # Testes unitários da aplicação
├── scripts/                                # Scripts auxiliares
├── terraform/aws/                          # Infraestrutura como Código
│   ├── modules/                            # Módulos Terraform reutilizáveis
│   │   ├── compute/                        # Módulo para instâncias EC2
│   │   │   ├── ami.tf                      # Configuração de AMIs
│   │   │   ├── iam.tf                      # Roles e políticas IAM
│   │   │   ├── jenkins_compute.tf          # Instâncias para Jenkins
│   │   │   ├── jenkins.sh                  # Script de inicialização Jenkins
│   │   │   ├── locals.tf                   # Variáveis locais
│   │   │   ├── outputs.tf                  # Outputs do módulo
│   │   │   ├── sonarqube_compute.tf        # Instâncias para SonarQube
│   │   │   ├── sonarqube.sh                # Script de inicialização SonarQube
│   │   │   └── variables.tf                # Variáveis do módulo
│   │   ├── devsecops/                      # Módulo DevSecOps
│   │   │   ├── monitoring/prometheus-grafana/  # Stack de monitoramento
│   │   │   │   ├── temp_build/             # Arquivos temporários de build
│   │   │   │   ├── build_monitoring_stack.tf   # Build da stack de monitoramento
│   │   │   │   ├── grafana_config.tf       # Configuração do Grafana
│   │   │   │   ├── grafana_dashboards.tf   # Dashboards do Grafana
│   │   │   │   ├── grafana_datasources.tf  # Fontes de dados do Grafana
│   │   │   │   ├── grafana_dockerfile.tf   # Dockerfile do Grafana
│   │   │   │   ├── monitoring_stack_aci.tf # Stack de monitoramento no ACI
│   │   │   │   ├── monitoring_stack_dockerfile.tf # Dockerfile da stack
│   │   │   │   ├── outputs.tf              # Outputs do monitoramento
│   │   │   │   ├── prometheus_alerts.tf    # Alertas do Prometheus
│   │   │   │   ├── prometheus_config.tf    # Configuração do Prometheus
│   │   │   │   └── variables.tf            # Variáveis do monitoramento
│   │   │   ├── pipeline/jenkins/           # Pipeline Jenkins
│   │   │   │   ├── main.tf                 # Configuração principal Jenkins
│   │   │   │   ├── outputs.tf              # Outputs do pipeline
│   │   │   │   └── variables.tf            # Variáveis do pipeline
│   │   │   ├── proxy-security/owasp-zap/   # Proxy de segurança OWASP ZAP
│   │   │   │   ├── temp_build/             # Arquivos temporários
│   │   │   │   ├── build_zap_image.tf      # Build da imagem ZAP
│   │   │   │   ├── outputs.tf              # Outputs do ZAP
│   │   │   │   ├── owasp_zap_aci.tf        # ZAP no Azure Container Instances
│   │   │   │   ├── variables.tf            # Variáveis do ZAP
│   │   │   │   ├── zap_dashboard_app.tf    # Dashboard do ZAP
│   │   │   │   ├── zap_dashboard_template.tf # Template do dashboard ZAP
│   │   │   │   ├── zap_dockerfile.tf       # Dockerfile do ZAP
│   │   │   │   └── zap_report_template.tf  # Template de relatório ZAP
│   │   │   ├── quality-assurance/sonarqube/ # Garantia de qualidade
│   │   │   │   ├── main.tf                 # Configuração principal SonarQube
│   │   │   │   ├── outputs.tf              # Outputs do SonarQube
│   │   │   │   └── variables.tf            # Variáveis do SonarQube
│   │   │   └── security-scanner/trivy/     # Scanner de segurança Trivy
│   │   │       ├── temp_build/             # Arquivos temporários
│   │   │       ├── build_trivy_image.tf    # Build da imagem Trivy
│   │   │       ├── outputs.tf              # Outputs do Trivy
│   │   │       ├── trivy_dashboard_aci.tf  # Dashboard Trivy no ACI
│   │   │       ├── trivy_dashboard_app.tf  # Aplicação dashboard Trivy
│   │   │       ├── trivy_dashboard_template.tf # Template dashboard Trivy
│   │   │       ├── trivy_dockerfile.tf     # Dockerfile do Trivy
│   │   │       ├── trivy_report_template.tf # Template relatório Trivy
│   │   │       └── variables.tf            # Variáveis do Trivy
│   │   ├── ecr/                            # Elastic Container Registry
│   │   │   ├── main.tf                     # Configuração principal ECR
│   │   │   ├── outputs.tf                  # Outputs do ECR
│   │   │   └── variables.tf                # Variáveis do ECR
│   │   ├── ecs/                            # Elastic Container Service
│   │   │   ├── main.tf                     # Configuração principal ECS
│   │   │   ├── outputs.tf                  # Outputs do ECS
│   │   │   └── variables.tf                # Variáveis do ECS
│   │   ├── elastic-ip/                     # IPs Elásticos
│   │   │   ├── main.tf                     # Configuração de IPs elásticos
│   │   │   ├── outputs.tf                  # Outputs dos IPs
│   │   │   └── variables.tf                # Variáveis dos IPs
│   │   ├── network/                        # Configuração de rede
│   │   │   ├── main.tf                     # Configuração principal da rede
│   │   │   ├── outputs.tf                  # Outputs da rede
│   │   │   └── variables.tf                # Variáveis da rede
│   │   └── security/                       # Configuração de segurança
│   │       ├── main.tf                     # Configuração principal segurança
│   │       ├── outputs.tf                  # Outputs de segurança
│   │       ├── providers.tf                # Provedores Terraform
│   │       └── variables.tf                # Variáveis de segurança
│   ├── main.tf                             # Arquivo principal Terraform
│   ├── outputs.tf                          # Outputs principais
│   └── variables.tf                        # Variáveis principais
├── .gitignore                              # Arquivos ignorados pelo Git
└── README.md                               # Documentação principal do projeto

```

Descrição dos Componentes Principais
------------------------------------

### 🏗️ **Terraform/AWS**

-   **Infraestrutura como Código** para provisionamento de recursos AWS
-   **Módulos reutilizáveis** para diferentes componentes da arquitetura
-   **Configuração modular** para facilitar manutenção e escalabilidade

### 🔧 **Sample-App**

-   **Aplicação Flask** de exemplo para demonstrar o pipeline
-   **Testes unitários** e configuração de qualidade de código
-   **Containerização** com Docker

### 🚀 **DevSecOps Pipeline**

-   **Jenkins** para CI/CD
-   **SonarQube** para análise de qualidade de código
-   **OWASP ZAP** para testes de segurança
-   **Trivy** para scanner de vulnerabilidades
-   **Prometheus/Grafana** para monitoramento

### 🔐 **Segurança Integrada**

-   **Análise estática** de código
-   **Testes de penetração** automatizados
-   **Scanner de vulnerabilidades** em containers
-   **Monitoramento** de segurança em tempo real

### ☁️ **Serviços AWS**

-   **EC2** para instâncias de compute
-   **ECR** para registry de containers
-   **ECS** para orquestração de containers
-   **VPC** para isolamento de rede
-   **Security Groups** para controle de acesso


🚀 Configuração da Infraestrutura
---------------------------------

### 1\. Preparar Backend do Terraform

bash

```
# Configurar S3 e DynamoDB para estado remoto
./scripts/setup-backend.sh
```

### 2\. Criar Chave SSH

bash

```
# Criar par de chaves na AWS
aws ec2 create-key-pair --key-name jenkins-key-dev\
    --query 'KeyMaterial' --output text > ~/.ssh/jenkins-key-dev.pem
chmod 400 ~/.ssh/jenkins-key-dev.pem
```

### 3\. Deploy da Infraestrutura

bash

```
cd terraform/
terraform init
terraform plan -var="key_name=jenkins-key-dev"
terraform apply -var="key_name=jenkins-key-dev" -auto-approve
```

### 4\. Recursos Criados

-   VPC com subnets públicas e privadas
-   Security Groups configurados
-   Instância EC2 com Jenkins
-   Instância EC2 com SonarQube
-   ECR Repository
-   ECS Cluster com Fargate
-   Application Load Balancer
-   Roles IAM necessários

⚙️ Configuração do Jenkins
--------------------------

<img src="/docs/images/pipeline-running.png" alt="Jenkins Pipeline">

### 1\. Acessar Jenkins

bash

```
# Obter URL do Jenkins
JENKINS_URL=$(terraform output -raw jenkins_url)
echo $JENKINS_URL

# Obter senha inicial
ssh -i ~/.ssh/jenkins-key-dev.pem ubuntu@<IP>\
    "sudo cat /var/lib/jenkins/secrets/initialAdminPassword"
```

### 2\. Configuração Inicial

1.  Acessar URL do Jenkins no navegador (<http://3.218.161.51:8080/>)
2.  Inserir senha inicial
3.  Instalar plugins sugeridos
4.  Criar usuário administrador

### 3\. Plugins Necessários

-   Docker Pipeline
-   Amazon ECR
-   Pipeline: AWS Steps
-   GitHub Integration
-   SonarQube Scanner
-   Blue Ocean (opcional)

### 4\. Configurar Credenciais

#### GitHub Token:

1.  GitHub → Settings → Developer settings → Personal access tokens
2.  Gerar novo token com permissões: `repo`, `workflow`
3.  No Jenkins: Manage Jenkins → Credentials → Add Credentials
    -   Kind: Username with password
    -   ID: `github-credentials`
    -   Username: seu-usuario-github
    -   Password: token-gerado

#### AWS Credentials:

1.  No Jenkins: Manage Jenkins → Credentials → Add Credentials
    -   Kind: Secret text
    -   ID: `aws-access-key-id`
    -   Secret: sua-access-key
2.  No Jenkins: Manage Jenkins → Credentials → Add Credentials
    -   Kind: Secret text
    -   ID: `aws-secret-access-key`
    -   Secret: sua-secret-key

#### SonarQube Token:

1.  No SonarQube: Administration → Security → Users → Tokens
2.  Gerar novo token com nome "jenkins-integration"
3.  No Jenkins: Manage Jenkins → Credentials → Add Credentials
    -   Kind: Secret text
    -   ID: `sonar-token`
    -   Secret: token-gerado-no-sonarqube

<img src="/docs/images/sonarqube-token.png" alt="Configuração do Token no SonarQube">

📊 Configuração do SonarQube
----------------------------

### 1\. Acesso ao SonarQube

O SonarQube está disponível em <http://35.171.200.117:9000> com as seguintes credenciais padrão:

-   Username: admin
-   Password: admin (alterada na primeira configuração)

### 2\. Configuração no Jenkins

1.  Instalar o plugin "SonarQube Scanner" no Jenkins
2.  Configurar o SonarQube no Jenkins:
    -   Manage Jenkins → System → SonarQube servers
    -   Nome: SonarQube
    -   URL do servidor: <http://35.171.200.117:9000>
    -   Token de autenticação: Selecionar credencial `sonar-token`

<img src="/docs/images/sonarqube-jenkins-config.png" alt="Configuração do SonarQube Scanner">

### 3\. Configuração do SonarQube Scanner

1.  Manage Jenkins → Tools → SonarQube Scanner installations
2.  Adicionar SonarQube Scanner:
    -   Nome: SonarScanner
    -   Versão: SonarQube Scanner 4.7.0.2747
    -   Selecionar "Instalar automaticamente"

- Para baixar a extensão, vá para "Extensões Disponíveis":
<img src="/docs/images/sonarqube-scanner-plugin.png" alt="Configuração do SonarQube Scanner Plugin">

- Configuração do SonarQube Scanner com o plugin instalado na plataforma Jenkins:
<img src="/docs/images/sonarqube-scanner-config.png" alt="Configuração do SonarQube Scanner Config">

### 4\. Configuração do Projeto no SonarQube

1.  Criar projeto no SonarQube com a chave "sample-app"
2.  Configurar arquivo `sonar-project.properties`:

properties

```
# Identificação do projeto
sonar.projectKey=sample-app
sonar.projectName=Sample App
sonar.projectVersion=1.0

# Configurações do servidor
sonar.host.url=http://35.171.200.117:9000

# Caminho para os arquivos de código
sonar.sources=.
sonar.python.coverage.reportPaths=coverage.xml
sonar.python.xunit.reportPath=test-results.xml

# Codificação do código-fonte
sonar.sourceEncoding=UTF-8
```

📦 Pipeline CI/CD
-----------------

O pipeline CI/CD é definido no Jenkinsfile e consiste nas seguintes etapas:

### 1\. Verificação de Acesso AWS

Verifica se o Jenkins tem acesso correto à AWS usando as credenciais configuradas.

### 2\. Análise de Código com SonarQube

Executa análise estática de código com SonarQube para identificar problemas de qualidade, vulnerabilidades e bugs.

### 3\. Build e Teste da Aplicação

Constrói a imagem Docker da aplicação e executa testes para verificar seu funcionamento.

### 4\. Push para ECR

Envia a imagem Docker para o Amazon ECR para armazenamento seguro.

### 5\. Deploy no ECS

Atualiza o serviço ECS com a nova imagem, implementando a aplicação em produção.

### Execução do Pipeline

Para executar o pipeline, você pode:

1.  Acessar o Jenkins e selecionar o projeto "sample-app-pipeline"
2.  Clicar em "Build Now"
3.  Acompanhar a execução nos logs ou na visualização de estágios

<img src="/docs/images/pipeline-running.png" alt="Jenkins Pipeline in platform">

- Pipeline executado:
<img src="/docs/images/jenkins-pipeline-with-sonarqube.png" alt="Jenkins Pipeline Executed with SonarQube">

🔍 Resultados da Análise de Código
----------------------------------

Após a execução do pipeline, é possível visualizar os resultados da análise de código no SonarQube:

- Dashboard de Qualidade de Software na plataforma do SonarQube:
<img src="/docs/images/sonarqube-dashboard.png" alt="SonarQube Dashboard">

- Pressione a linha de informações para ver os detalhes da inspeção de qualidade:
<img src="/docs/images/sonarqube-dashboard-details.png" alt="SonarQube Details Dashboard">

Os resultados atuais mostram:

-   0 Bugs detectados
-   0 Vulnerabilidades de segurança
-   2 Security Hotspots para revisão (0.0% revisados)
-   0 Code Smells
-   0% de Cobertura de código em 40 linhas
-   0% de Duplicação de código em 209 linhas

Todos os Quality Gates foram aprovados, resultando em status "Passed".

🚢 Deploy da Aplicação
----------------------

### 1\. Verificar Implantação

bash

```
# Verificar status do serviço ECS
aws ecs describe-services\
    --cluster jenkins-cicd-dev\
    --services jenkins-cicd-dev\
    --region us-east-1
```

### 2\. Testar Aplicação

A aplicação está disponível no URL do Application Load Balancer:

bash

```
# Acessar endpoint de saúde
curl http://jenkins-cicd-dev-alb-2039113869.us-east-1.elb.amazonaws.com/health
```

📊 Monitoramento
----------------

### CloudWatch Logs

bash

```
# Ver logs do ECS
aws logs tail /ecs/jenkins-cicd-dev --follow
```

### CloudWatch Metrics

-   CPU e Memória do ECS
-   Latência do ALB
-   Healthy hosts no Target Group

### Health Checks

-   Jenkins: <http://3.218.161.51:8080>
-   SonarQube: <http://35.171.200.117:9000>
-   Aplicação: <http://jenkins-cicd-dev-alb-2039113869.us-east-1.elb.amazonaws.com/health>

🔍 Troubleshooting
------------------

### Problemas com o SonarQube

Se o SonarScanner não estiver funcionando:

1.  Verificar se o plugin do SonarQube está instalado no Jenkins
2.  Confirmar se as credenciais do token SonarQube estão corretas
3.  Verificar conectividade entre Jenkins e SonarQube
4.  Examinar os logs do SonarQube:

    bash

    ```
    docker logs sonarqube-container
    ```

### Pipeline falhando

1.  Verificar acesso às credenciais da AWS
2.  Confirmar permissões corretas do IAM
3.  Verificar conectividade de rede entre Jenkins, SonarQube e AWS
4.  Examinar os logs do pipeline para identificar o ponto de falha

### ECS não inicia tasks

bash

```
# Ver eventos do service
aws ecs describe-services\
    --cluster jenkins-cicd-dev\
    --services jenkins-cicd-dev\
    --query 'services[0].events[:5]'

# Verificar logs do container
aws logs get-log-events\
    --log-group-name /ecs/jenkins-cicd-dev\
    --log-stream-name <STREAM_NAME>
```

🔐 Segurança
------------

### Boas Práticas Implementadas:

-   Secrets gerenciados como credenciais no Jenkins
-   IAM roles com princípio do menor privilégio
-   Security Groups restritivos
-   Análise de segurança com SonarQube
-   Escaneamento de vulnerabilidades em imagens Docker

### Melhorias Recomendadas:

-   Implementar VPN para acesso ao Jenkins
-   Usar AWS Secrets Manager para senhas
-   Habilitar MFA para usuários Jenkins
-   Implementar network policies no ECS

🤝 Contribuindo
---------------

1.  Fork o projeto
2.  Crie uma branch para sua feature (`git checkout -b feature/AmazingFeature`)
3.  Commit suas mudanças (`git commit -m 'Add some AmazingFeature'`)
4.  Push para a branch (`git push origin feature/AmazingFeature`)
5.  Abra um Pull Request

📝 Licença
----------

Este projeto está sob a licença MIT. Veja o arquivo `LICENSE` para mais detalhes.

👥 Autores
----------

-   **ndevops25** - *Trabalho Inicial* - [ndevops25](https://github.com/ndevops25)

🙏 Agradecimentos
-----------------

-   Professor pela orientação
-   Colegas de turma pelo apoio
-   Documentação da AWS, Jenkins e SonarQube

* * * * *

**Nota**: Este projeto foi desenvolvido como parte do trabalho CP2 e CP3 - Construção de um Pipeline CI/CD usando Jenkins e SonarQube.
