# Estudo de Caso 7: Arquitetura de Aplicativos - Guia de Implementação para Certificação

Este guia detalha o **passo a passo de implementação** para projetar uma arquitetura de e-commerce moderna baseada em **microsserviços**, **conteneirização** e **dados híbridos** no Azure, focando em **escalabilidade** e **alta disponibilidade**.

## Cenário e Requisitos Chave

O cenário exige uma aplicação de e-commerce (Catálogo, Carrinho, Checkout) que utilize dados relacionais e não relacionais, com requisitos críticos de **auto-scaling rápido** e **alta disponibilidade**.

| Requisito | Objetivo de Implementação | Ferramenta Chave |
| :--- | :--- | :--- |
| **Arquitetura de Microsserviços** | Dividir a aplicação em serviços independentes. | Azure Container Apps |
| **Dados Híbridos** | Usar o banco de dados certo para cada tipo de dado. | Azure SQL DB + Azure Cosmos DB + Azure Cache for Redis |
| **Auto-scaling Rápido** | Escalabilidade elástica e baseada em eventos. | Azure Container Apps (KEDA) |
| **Processamento Assíncrono** | Desacoplar o checkout de tarefas secundárias (e-mail, logística). | Azure Service Bus + Azure Functions |

## Passo a Passo de Implementação (Arquitetura de Microsserviços)

A solução utiliza uma combinação de serviços PaaS e Serverless para máxima agilidade e resiliência.

### Fase 1: Definição da Camada de Dados Híbrida

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **1.1** | **Dados Transacionais (Pedidos)** | Use o **Azure SQL Database** (PaaS) para dados transacionais (pedidos, clientes, pagamentos) que exigem garantias ACID e integridade referencial. | Azure SQL Database |
| **1.2** | **Dados Flexíveis (Catálogo)** | Use o **Azure Cosmos DB** (NoSQL) para o catálogo de produtos, aproveitando seu esquema flexível e baixa latência global. | Azure Cosmos DB |
| **1.3** | **Dados Voláteis (Carrinho)** | Use o **Azure Cache for Redis** (cache em memória) para armazenar o estado do carrinho de compras e sessões de usuário, garantindo acesso ultrarrápido. | Azure Cache for Redis |

### Fase 2: Configuração da Camada de Computação (Microsserviços)

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **2.1** | **Conteneirização** | Empacote cada microsserviço (Catálogo, Carrinho, Pedidos) em contêineres Docker. | Docker / Azure Container Registry (ACR) |
| **2.2** | **Orquestração** | Implante os contêineres no **Azure Container Apps**. Esta plataforma gerencia a orquestração (Kubernetes/KEDA) de forma simplificada. | Azure Container Apps |
| **2.3** | **Auto-scaling** | Configure o **KEDA (Kubernetes Event-driven Autoscaling)** nativo do Container Apps para escalar os microsserviços com base em métricas (ex: requisições HTTP, profundidade da fila). | Azure Container Apps |

### Fase 3: Segurança e Ponto de Entrada

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **3.1** | **Proteção de Borda** | Implante um **Azure Application Gateway** na frente dos microsserviços. | Azure Application Gateway |
| **3.2** | **Web Application Firewall (WAF)** | Habilite o **WAF** no Application Gateway para proteger a aplicação contra ataques comuns da web (SQL Injection, XSS). | Azure Application Gateway WAF |
| **3.3** | **Roteamento** | Use o Application Gateway para rotear o tráfego para os microsserviços apropriados (ex: `/catalogo` para o microsserviço de Catálogo). | Application Gateway |

### Fase 4: Processamento Assíncrono (Checkout)

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **4.1** | **Mensageria** | Use o **Azure Service Bus** para desacoplar o microsserviço de Pedidos das tarefas de *backend* (e-mail, logística). | Azure Service Bus |
| **4.2** | **Publicação de Evento** | Após a conclusão do pedido, o microsserviço de Pedidos publica uma mensagem (`PedidoRealizado`) no Service Bus. | Código da Aplicação |
| **4.3** | **Processamento de Tarefas** | Crie **Azure Functions** (Serverless) para escutar a fila/tópico do Service Bus. Cada função é responsável por uma tarefa específica (ex: `FuncaoEnviarEmail`, `FuncaoNotificarLogistica`). | Azure Functions |

## Resumo da Arquitetura (Justificativa para Certificação)

A arquitetura de microsserviços no **Azure Container Apps** com **Dados Híbridos** é a melhor prática para este cenário, pois:

*   **Resiliência:** A falha de um microsserviço (ex: Catálogo) não derruba o sistema de Pedidos.
*   **Escalabilidade:** Cada serviço escala de forma independente, otimizando o custo. O Container Apps fornece o auto-scaling rápido necessário para picos de tráfego.
*   **Desacoplamento:** O uso do **Service Bus** garante que o processo de checkout seja rápido, movendo tarefas demoradas para o processamento assíncrono via **Azure Functions**.

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
