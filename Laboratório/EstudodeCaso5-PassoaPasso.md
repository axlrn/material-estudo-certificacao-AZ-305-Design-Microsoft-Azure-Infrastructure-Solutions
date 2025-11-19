# Estudo de Caso 5: Dados Relacionais - Guia de Implementação para Certificação

Este guia detalha o **passo a passo de implementação** para hospedar um banco de dados relacional (SQL Server) no Azure, focando em **alta disponibilidade**, **segurança** e **mínimo gerenciamento** para uma aplicação web crítica.

## Cenário e Requisitos Chave

O cenário exige uma solução de banco de dados relacional (SQL Server) para um aplicativo web .NET Core, com o requisito **crítico** de **alta disponibilidade** (HA), similar ao que seria obtido com um *Always On Availability Group* em um ambiente *on-premises*.

| Requisito | Objetivo de Implementação | Ferramenta Chave |
| :--- | :--- | :--- |
| **Banco de Dados Relacional** | Suportar o catálogo de produtos (SQL Server). | Azure SQL Database |
| **Alta Disponibilidade (HA)** | Garantir resiliência e failover automático e rápido. | Azure SQL Database (Camada Business Critical) |
| **Segurança de Conexão** | Proteger a *connection string* e o acesso ao banco de dados. | Azure Key Vault e Private Link |
| **Mínimo Gerenciamento** | Evitar a sobrecarga de gerenciar o SO e o cluster de HA. | Modelo PaaS (Platform as a Service) |

## Passo a Passo de Implementação (Azure SQL Database - PaaS)

A decisão estratégica é usar o **Azure SQL Database** no modelo PaaS, que oferece HA nativa sem a complexidade de gerenciar VMs e clusters.

### Fase 1: Configuração do Banco de Dados e HA

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **1.1** | **Criação do Azure SQL Database** | Crie um novo servidor lógico e um banco de dados. | Azure SQL Database |
| **1.2** | **Seleção da Camada de Serviço** | Escolha a camada **Business Critical** (Crítico para os Negócios). Esta camada usa tecnologia semelhante ao *Always On* (4 réplicas sincronizadas) para fornecer o mais alto SLA (99,995%) e failover automático. | Azure SQL Database (Business Critical) |
| **1.3** | **Configuração de Backups** | Verifique a política de retenção de backups automáticos (ponto-a-ponto no tempo) e configure backups de longo prazo (LTR) se necessário. | Azure SQL Database (Backups) |

### Fase 2: Segurança e Conectividade

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **2.1** | **Hospedagem da Aplicação** | Implante o aplicativo web .NET Core no **Azure App Service** (PaaS). | Azure App Service |
| **2.2** | **Armazenamento Seguro de Credenciais** | Armazene a *connection string* do banco de dados no **Azure Key Vault** como um segredo. | Azure Key Vault |
| **2.3** | **Acesso Seguro da Aplicação** | Configure o Azure App Service para usar uma **Identidade Gerenciada (Managed Identity)** para ler o segredo do Key Vault em tempo de execução. | Azure App Service / Managed Identity |
| **2.4** | **Isolamento de Rede** | Configure um **Ponto de Extremidade Privado (Private Endpoint)** para o Azure SQL Database. Isso garante que o tráfego entre o App Service e o banco de dados viaje pela rede privada do Azure, sem exposição à internet pública. | Azure Private Link |

### Fase 3: Otimização e Monitoramento

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **3.1** | **Otimização de Consultas** | Utilize o **Query Performance Insight** e o **Automatic Tuning** do Azure SQL Database para identificar e otimizar consultas lentas e índices. | Azure SQL Database |
| **3.2** | **Monitoramento** | Monitore métricas chave (CPU, I/O, latência) usando o **Azure Monitor** para garantir que o desempenho atenda aos requisitos da aplicação. | Azure Monitor |
| **3.3** | **Recuperação de Desastres (DR)** | Para proteção regional, configure um **Grupo de Failover Automático (Auto-Failover Group)** para replicar o banco de dados para uma região secundária. | Azure SQL Database (Failover Groups) |

## Justificativa para a Certificação (PaaS vs. IaaS)

A escolha do **Azure SQL Database (PaaS)** em vez do **SQL Server em VMs (IaaS)** é a melhor prática para aplicações modernas e críticas, pois:

*   **Menor TCO (Custo Total de Propriedade):** O Azure gerencia o SO, patches, backups e a complexa configuração de HA (Always On), reduzindo drasticamente a sobrecarga operacional e o custo de pessoal.
*   **HA Simplificada:** A camada *Business Critical* fornece HA nativa e automática com um SLA superior, sem a necessidade de configurar manualmente o *Windows Clustering* e o *Always On*.
*   **Foco no Desenvolvimento:** A equipe de desenvolvimento pode se concentrar no código da aplicação, em vez de gerenciar a infraestrutura do banco de dados.

---

# Estudo de Caso 6: Estoque Just-in-Time - Guia de Implementação para Certificação

## Cenário e Requisitos Chave

O Estudo de Caso 6 envolve a criação de um sistema de estoque *Just-in-Time* (JIT), que exige **processamento de eventos em tempo real**, **mensageria confiável** e **integração desacoplada** entre os sistemas.

| Requisito | Objetivo de Implementação | Ferramenta Chave |
| :--- | :--- | :--- |
| **Mensageria Confiável** | Garantir que as mensagens de pedido e estoque sejam entregues e processadas, mesmo sob falha. | Azure Service Bus |
| **Processamento Assíncrono** | Desacoplar o sistema de pedidos do sistema de estoque. | Azure Service Bus (Topics/Queues) |
| **Escalabilidade de Eventos** | Lidar com um grande volume de eventos de estoque e pedidos. | Azure Functions |

## Passo a Passo de Implementação (Azure)

A solução utiliza serviços de mensageria e processamento de eventos, como **Azure Service Bus** e **Azure Functions**.

### Fase 1: Configuração da Mensageria (Sistema de Pedidos -> Estoque)

O **Azure Service Bus** é ideal para mensageria transacional e desacoplamento.

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **1.1** | **Criação do Namespace** | Crie um *Namespace* do Service Bus (nível *Standard* ou *Premium* para recursos avançados). | Azure Service Bus |
| **1.2** | **Criação da Fila/Tópico** | Crie um **Tópico** (Topic) para mensagens de "Novo Pedido". O Tópico permite que múltiplos sistemas (ex: Estoque, Faturamento) se inscrevam. | Azure Service Bus Topic |
| **1.3** | **Configuração de Resiliência** | Habilite o **Dead-Lettering** (DLQ) no Tópico/Assinatura para garantir que mensagens que falhem no processamento sejam isoladas para análise manual. | Azure Service Bus (Assinatura) |
| **1.4** | **Envio de Mensagens** | O sistema de pedidos envia uma mensagem (ex: JSON com `idDoPedido`, `idDoProduto`, `quantidade`) para o Tópico. | Código da Aplicação (SDK do Service Bus) |

### Fase 2: Processamento Assíncrono do Estoque

O **Azure Functions** é ideal para processar eventos de forma escalável e sem servidor.

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **2.1** | **Criação do Azure Function** | Crie um *Function App* (Plano *Consumption* para JIT/sem servidor). | Azure Functions |
| **2.2** | **Criação da Função de Processamento** | Crie uma função com um *Trigger* (Gatilho) do **Azure Service Bus**. Esta função será acionada automaticamente sempre que uma nova mensagem chegar na Assinatura do Tópico. | Azure Functions (Service Bus Trigger) |
| **2.3** | **Lógica de Estoque** | A função executa a lógica de atualização do estoque (ex: decrementa a quantidade no banco de dados relacional ou NoSQL). | Código da Função |
| **2.4** | **Confirmação da Mensagem** | Se o processamento for bem-sucedido, a função confirma a mensagem, removendo-a da fila. Se falhar, a mensagem é movida para a DLQ. | Código da Função |

### Fase 3: Monitoramento e Alerta

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **3.1** | **Monitoramento de Filas** | Monitore o número de mensagens ativas e, crucialmente, o número de mensagens na **Dead-Letter Queue (DLQ)**. | Azure Monitor / Service Bus Metrics |
| **3.2** | **Alerta de DLQ** | Configure um alerta para notificar a equipe de operações se o número de mensagens na DLQ exceder um limite (ex: 5 mensagens). | Azure Monitor (Alertas) |
| **3.3** | **Monitoramento de Funções** | Monitore as execuções e erros do Azure Function para garantir que o processamento de estoque esteja saudável. | Application Insights |

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
