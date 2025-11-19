# Estudo de Caso 6: Estoque Just-in-Time - Guia de Implementação para Certificação

Este guia detalha o **passo a passo de implementação** para construir uma plataforma de dados de **Estoque Just-in-Time (JIT)** no Azure, focando em **análise em tempo real** e **Recuperação de Desastres (DR)** em múltiplas regiões.

## Cenário e Requisitos Chave

O cenário exige uma arquitetura de Big Data que suporte a ingestão de dados em alta velocidade e garanta a continuidade dos negócios (DR) em caso de falha de uma região inteira.

| Requisito | Objetivo de Implementação | Ferramenta Chave |
| :--- | :--- | :--- |
| **Ingestão de Eventos em Tempo Real** | Capturar milhões de eventos por segundo de forma confiável. | Azure Event Hubs |
| **Armazenamento de Big Data** | Repositório centralizado, escalável e de baixo custo para todos os tipos de dados. | Azure Data Lake Storage Gen2 (ADLS Gen2) |
| **Processamento e Análise** | Ferramentas unificadas para ETL/ELT e consultas de dados. | Azure Synapse Analytics (Spark e SQL Pools) |
| **Recuperação de Desastres (DR)** | Garantir a continuidade da operação em caso de falha regional. | ADLS Gen2 (RA-GZRS) + Failover de Event Hubs |

## Passo a Passo de Implementação (Azure Data Platform)

A solução é baseada no **Azure Synapse Analytics** como o *hub* de processamento e no **ADLS Gen2** como o *data lake*.

### Fase 1: Configuração da Ingestão de Dados em Tempo Real

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **1.1** | **Criação do Event Hubs** | Crie um **Azure Event Hubs** para atuar como a porta de entrada para dados de alta velocidade (ex: leituras de sensores, pedidos). | Azure Event Hubs |
| **1.2** | **Captura de Dados** | Configure o recurso **Capture** do Event Hubs para enviar automaticamente os dados brutos para o **ADLS Gen2** (Zona Bronze). | Event Hubs Capture |

### Fase 2: Configuração do Armazenamento e Organização (Data Lake)

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **2.1** | **Criação do ADLS Gen2** | Crie uma conta de armazenamento **ADLS Gen2** (Hierarchical Namespace habilitado). | Azure Data Lake Storage Gen2 |
| **2.2** | **Organização em Zonas** | Estruture o Data Lake em três zonas lógicas: **Bronze** (dados brutos), **Silver** (dados limpos/enriquecidos) e **Gold** (dados agregados para consumo). | ADLS Gen2 (Pastas) |

### Fase 3: Processamento e Análise (Azure Synapse Analytics)

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **3.1** | **Criação do Workspace Synapse** | Crie um **Azure Synapse Workspace** e conecte-o ao ADLS Gen2. | Azure Synapse Analytics |
| **3.2** | **Transformação de Dados (ETL/ELT)** | Use o **Serverless Apache Spark Pool** (via Notebooks) para ler dados da Zona Bronze, executar transformações complexas e salvar os resultados na Zona Silver (ex: formato Parquet otimizado). | Synapse Spark Pool |
| **3.3** | **Consulta de Consumo** | Use o **Serverless SQL Pool** para consultar diretamente os dados na Zona Gold (dados agregados), expondo-os para ferramentas de BI. | Synapse SQL Pool |

### Fase 4: Implementação da Recuperação de Desastres (DR)

A DR é crucial para um sistema JIT. A estratégia é **Ativo-Passivo** em duas regiões.

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **4.1** | **DR do Armazenamento (ADLS)** | Configure o ADLS Gen2 com **RA-GZRS (Read-Access Geo-Zone-Redundant Storage)**. Isso replica os dados assincronamente para uma região secundária e permite a leitura de lá. | ADLS Gen2 (Redundância) |
| **4.2** | **DR da Ingestão (Event Hubs)** | Configure o **Geo-disaster recovery** para o Event Hubs, emparelhando um *Namespace* primário (ativo) com um secundário (passivo). | Azure Event Hubs |
| **4.3** | **DR do Processamento (Synapse)** | Implante um **Synapse Workspace** na região secundária. Mantenha os pipelines e notebooks sincronizados (via Git) entre os dois workspaces. | Azure Synapse Analytics |
| **4.4** | **Failover (Passo a Passo)** | Em caso de falha regional: 1) Executar o **failover** do ADLS Gen2 (o secundário se torna gravável). 2) Executar o **failover** do Event Hubs. 3) Ativar os pipelines no **Synapse Workspace** secundário. 4) Redirecionar o **Power BI** para o Synapse secundário. | Equipe de Operações / Azure Portal |

---

# Estudo de Caso 7: Arquitetura de Aplicativos - Guia de Implementação para Certificação

## Cenário e Requisitos Chave

O Estudo de Caso 7 exige a definição de uma arquitetura de aplicação moderna, focando em **microserviços**, **conteneirização** e **orquestração** para alta escalabilidade e agilidade no desenvolvimento.

| Requisito | Objetivo de Implementação | Ferramenta Chave |
| :--- | :--- | :--- |
| **Microserviços** | Dividir a aplicação em serviços menores e independentes. | Docker |
| **Conteneirização** | Empacotar cada serviço com suas dependências para portabilidade. | Azure Container Registry (ACR) |
| **Orquestração** | Gerenciar o ciclo de vida, escalabilidade e rede dos contêineres. | Azure Kubernetes Service (AKS) |
| **CI/CD** | Implementar um pipeline de integração e entrega contínua. | Azure DevOps / GitHub Actions |

## Passo a Passo de Implementação (Azure)

A solução utiliza **Docker**, **Azure Kubernetes Service (AKS)** e **Azure DevOps/GitHub Actions**.

### Fase 1: Conteneirização dos Microserviços

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **1.1** | **Definição de *Dockerfiles*** | Crie um *Dockerfile* para cada microserviço, definindo o ambiente de execução e as dependências. | Docker |
| **1.2** | **Criação do Registro de Contêineres** | Crie um **Azure Container Registry (ACR)** privado para armazenar as imagens Docker construídas. | Azure Container Registry (ACR) |
| **1.3** | **Construção e *Push* das Imagens** | Construa as imagens Docker localmente ou via pipeline de CI e envie (*push*) para o ACR. | Docker CLI / Pipeline de CI |

### Fase 2: Orquestração com Azure Kubernetes Service (AKS)

O AKS é o orquestrador de contêineres gerenciado do Azure.

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **2.1** | **Criação do Cluster AKS** | Crie um cluster AKS, configurando o tamanho e o número de *Node Pools* (Pools de Nós) para hospedar os contêineres. | Azure Kubernetes Service (AKS) |
| **2.2** | **Configuração de Acesso ao ACR** | Configure o AKS para autenticar e puxar imagens do **Azure Container Registry (ACR)**. | AKS (Integração com ACR) |
| **2.3** | **Definição de Implantação (*Deployment*)** | Crie arquivos de manifesto Kubernetes (YAML) para cada microserviço, definindo o número de réplicas (*replicas*) e os limites de recursos. | Kubernetes YAML |
| **2.4** | **Configuração de Serviço (*Service*)** | Crie um *Service* do tipo `LoadBalancer` ou `ClusterIP` para expor os microserviços. Use um **Ingress Controller** para roteamento HTTP/HTTPS. | Kubernetes Service / Ingress |

### Fase 3: Implementação de CI/CD

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **3.1** | **Pipeline de CI (Integração Contínua)** | Configure um pipeline para: 1) Receber o código (ex: GitHub), 2) Construir a imagem Docker, 3) Executar testes unitários, 4) Enviar a imagem para o ACR. | Azure DevOps / GitHub Actions |
| **3.2** | **Pipeline de CD (Entrega Contínua)** | Configure um pipeline para: 1) Monitorar novas imagens no ACR, 2) Atualizar o manifesto Kubernetes com a nova tag da imagem, 3) Aplicar o manifesto ao cluster AKS (ex: `kubectl apply -f`). | Azure DevOps / Flux/ArgoCD (GitOps) |

---

# Estudo de Caso 8: Médio - Migração Híbrida e Modernização de Aplicação Interna - Guia de Implementação para Certificação

## Cenário e Requisitos Chave

O Estudo de Caso 8 envolve a migração de uma aplicação interna legada para a nuvem, mantendo a conectividade com sistemas *on-premises* (abordagem **Híbrida**) e modernizando a aplicação.

| Requisito | Objetivo de Implementação | Ferramenta Chave |
| :--- | :--- | :--- |
| **Conectividade Híbrida** | Estabelecer uma conexão segura e privada entre a rede *on-premises* e a nuvem. | Azure VPN Gateway / ExpressRoute |
| **Migração de Aplicação** | Mover a aplicação interna para um ambiente de nuvem moderno. | Azure App Service / AKS |
| **Modernização** | Conteneirizar a aplicação para maior agilidade. | Docker / ACR |

## Passo a Passo de Implementação (Azure)

A solução combina **Azure VPN Gateway/ExpressRoute** para conectividade e **Azure App Service/AKS** para modernização.

### Fase 1: Estabelecimento da Conectividade Híbrida

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **1.1** | **Criação da VNet (Rede Virtual)** | Crie uma **Rede Virtual (VNet)** no Azure com um espaço de endereçamento que não se sobreponha à rede *on-premises*. | Azure Virtual Network |
| **1.2** | **Escolha da Conexão Híbrida** | **Opção 1 (VPN Gateway):** Crie um **VPN Gateway** (Site-to-Site) para uma conexão segura via internet. **Opção 2 (ExpressRoute):** Use **ExpressRoute** para uma conexão privada e dedicada (maior largura de banda e SLA). | Azure VPN Gateway / Azure ExpressRoute |
| **1.3** | **Configuração da Conexão** | Configure o dispositivo de VPN *on-premises* para se conectar ao Azure VPN Gateway ou trabalhe com o provedor para configurar o ExpressRoute. | Azure Portal / Dispositivo de Rede Local |

### Fase 2: Migração e Modernização da Aplicação

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **2.1** | **Conteneirização** | Conteneirize a aplicação interna usando Docker (conforme Estudo de Caso 7). | Docker |
| **2.2** | **Hospedagem da Aplicação** | Implante o contêiner em um serviço de hospedagem que possa se conectar à VNet (ex: **Azure App Service com VNet Integration** ou **AKS**). | Azure App Service / AKS |
| **2.3** | **Conectividade com Sistemas Legados** | Garanta que a aplicação na nuvem possa acessar os sistemas *on-premises* (ex: banco de dados legado, *mainframes*) através da conexão híbrida (VPN/ExpressRoute). | VNet Integration |

### Fase 3: Migração de Dados (Se Necessário)

Se o banco de dados também for migrado, use ferramentas de migração.

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **3.1** | **Avaliação de Banco de Dados** | Use o **Azure Migrate** para avaliar a complexidade da migração do banco de dados legado. | Azure Migrate |
| **3.2** | **Migração de Dados** | Use o **Azure Database Migration Service (DMS)** para migrar o banco de dados com tempo de inatividade mínimo. | Azure DMS |

---

# Estudo de Caso 9: Autenticação e Autorização - Guia de Implementação para Certificação

## Cenário e Requisitos Chave

O Estudo de Caso 9 foca na implementação de um sistema robusto de **Autenticação** (quem é o usuário) e **Autorização** (o que o usuário pode fazer) para uma aplicação.

| Requisito | Objetivo de Implementação | Ferramenta Chave |
| :--- | :--- | :--- |
| **Identidade Centralizada** | Usar um provedor de identidade único para todos os usuários. | Microsoft Entra ID (Azure AD) |
| **Autenticação Segura** | Implementar padrões modernos (ex: OAuth 2.0, OpenID Connect). | MSAL Library |
| **Controle de Acesso Baseado em Função (RBAC)** | Definir permissões com base no papel do usuário. | Microsoft Entra ID (Roles/Claims) |

## Passo a Passo de Implementação (Azure)

A solução utiliza o **Microsoft Entra ID** (antigo Azure Active Directory) como provedor de identidade.

### Fase 1: Configuração do Provedor de Identidade

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **1.1** | **Registro da Aplicação** | Registre a aplicação no **Microsoft Entra ID** para obter um `Application ID` e configurar *Redirect URIs*. | Microsoft Entra ID (Registros de Aplicativos) |
| **1.2** | **Configuração de Segredos** | Crie um segredo de cliente (*Client Secret*) ou use certificados para que a aplicação possa se autenticar no Entra ID. | Microsoft Entra ID (Certificados e Segredos) |
| **1.3** | **Configuração de Permissões** | Defina as permissões (*scopes*) que a aplicação solicitará aos usuários (ex: acesso ao perfil, e-mail). | Microsoft Entra ID (Permissões de API) |

### Fase 2: Implementação da Autenticação (Login)

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **2.1** | **Fluxo de Autenticação** | Implemente o fluxo **OAuth 2.0/OpenID Connect** (ex: *Authorization Code Flow*) na aplicação. | Código da Aplicação (MSAL Library) |
| **2.2** | **Obtenção de *Tokens*** | Após o login, a aplicação recebe um **ID Token** (para informações do usuário) e um **Access Token** (para acessar APIs). | Código da Aplicação |
| **2.3** | **Validação de *Tokens*** | O *back-end* da aplicação deve validar a assinatura e a validade do *Access Token* antes de conceder acesso a qualquer recurso. | Código da Aplicação (Middleware) |

### Fase 3: Implementação da Autorização (RBAC)

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **3.1** | **Definição de Funções (Roles)** | Defina as funções da aplicação (ex: `Admin`, `Gerente`, `Usuário`) no **Microsoft Entra ID** (Manifesto da Aplicação). | Microsoft Entra ID (Manifesto) |
| **3.2** | **Atribuição de Funções** | Atribua usuários às funções definidas. | Microsoft Entra ID (Usuários e Grupos) |
| **3.3** | **Verificação de Função no Código** | O Entra ID inclui as funções atribuídas no *Access Token* (via *claims*). O *back-end* verifica a *claim* de função para decidir se o usuário tem permissão para executar uma ação específica. | Código da Aplicação (Verificação de Claims) |

---

# Estudo de Caso 10: Logging - Guia de Implementação para Certificação

## Cenário e Requisitos Chave

O Estudo de Caso 10 exige um sistema centralizado de **Logging** (registro de eventos), **Monitoramento** e **Análise** para garantir a saúde e a segurança da aplicação.

| Requisito | Objetivo de Implementação | Ferramenta Chave |
| :--- | :--- | :--- |
| **Centralização de Logs** | Coletar logs de todas as fontes (aplicação, infraestrutura, segurança) em um único local. | Azure Monitor (Log Analytics) |
| **Análise e Busca** | Capacidade de buscar, filtrar e analisar logs rapidamente. | Kusto Query Language (KQL) |
| **Monitoramento e Alerta** | Criar painéis de monitoramento e alertas proativos. | Azure Monitor (Alertas e Dashboards) |

## Passo a Passo de Implementação (Azure)

A solução utiliza o **Azure Monitor** e o **Log Analytics Workspace** como a espinha dorsal.

### Fase 1: Configuração do Workspace de Logs

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **1.1** | **Criação do Log Analytics Workspace** | Crie um **Log Analytics Workspace** centralizado. Este será o repositório de todos os logs. | Azure Monitor (Log Analytics) |
| **1.2** | **Configuração de Retenção** | Defina a política de retenção de dados (ex: 90 dias) para atender aos requisitos de conformidade e custo. | Log Analytics Workspace |

### Fase 2: Coleta de Logs e Métricas

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **2.1** | **Logs de Infraestrutura** | Configure as **Configurações de Diagnóstico (Diagnostic Settings)** para enviar logs de serviços do Azure (ex: App Service, SQL Database, Key Vault) para o Log Analytics Workspace. | Azure Monitor (Diagnostic Settings) |
| **2.2** | **Logs de Aplicação** | Integre a aplicação com o **Application Insights**. Isso coleta logs de código, rastreamento de requisições, métricas de desempenho e exceções. | Application Insights |
| **2.3** | **Logs de Segurança** | Envie logs de segurança (ex: *Activity Logs*, logs de firewall) para o Workspace. | Azure Monitor / Azure Security Center |

### Fase 3: Análise, Monitoramento e Alerta

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **3.1** | **Análise de Logs (KQL)** | Use a linguagem de consulta **Kusto Query Language (KQL)** no Log Analytics para buscar, filtrar e correlacionar logs de diferentes fontes. | Log Analytics (KQL) |
| **3.2** | **Criação de Painéis (*Dashboards*)** | Crie painéis no Azure Monitor para visualizar métricas chave (ex: taxa de erro, latência, uso de CPU) e consultas KQL importantes. | Azure Monitor Dashboards |
| **3.3** | **Configuração de Alertas** | Crie **Regras de Alerta** baseadas em: 1) **Métricas** (ex: CPU > 90% por 5 minutos) e 2) **Logs** (ex: mais de 100 erros de nível `Critical` em 1 minuto). | Azure Monitor (Alertas) |
| **3.4** | **Grupos de Ação** | Configure **Grupos de Ação (Action Groups)** para definir quem deve ser notificado (e-mail, SMS) ou qual automação deve ser executada (Webhook, Azure Function) quando um alerta for disparado. | Azure Monitor (Action Groups) |
