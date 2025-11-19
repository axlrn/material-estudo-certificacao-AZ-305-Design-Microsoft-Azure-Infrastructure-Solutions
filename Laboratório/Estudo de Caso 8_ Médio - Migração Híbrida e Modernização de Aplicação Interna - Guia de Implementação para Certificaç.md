# Estudo de Caso 8: Médio - Migração Híbrida e Modernização de Aplicação Interna - Guia de Implementação para Certificação

Este guia detalha o **passo a passo de implementação** para migrar uma aplicação interna crítica para o Azure, estabelecendo uma **conectividade híbrida resiliente** e modernizando a arquitetura de rede com o modelo **Hub-Spoke**.

## Cenário e Requisitos Chave

O cenário exige a migração de uma aplicação de BI de 3 camadas (*on-premises* para Azure), mantendo a comunicação segura e de baixa latência com um sistema ERP que permanece *on-premises*.

| Requisito | Objetivo de Implementação | Ferramenta Chave |
| :--- | :--- | :--- |
| **Conectividade Híbrida Resiliente** | Conexão segura e com failover automático entre *on-premises* e Azure. | ExpressRoute + VPN Gateway (Backup) |
| **Gerenciamento Centralizado** | Centralizar a segurança e o roteamento. | Topologia Hub-Spoke |
| **Segurança e Inspeção de Tráfego** | Filtrar todo o tráfego de entrada, saída e leste-oeste. | Azure Firewall |
| **Segmentação de Rede** | Isolar as camadas da aplicação (Web, App, DB). | NSG (Network Security Groups) + ASG (Application Security Groups) |

## Passo a Passo de Implementação (Arquitetura Híbrida Hub-Spoke)

A solução se baseia na criação de uma rede Hub-Spoke e na implementação de segurança em profundidade.

### Fase 1: Configuração da Topologia Hub-Spoke

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **1.1** | **Criação da VNet Hub** | Crie a VNet Hub para hospedar os serviços de rede compartilhados (Gateways, Firewall). | Azure Virtual Network |
| **1.2** | **Criação da VNet Spoke** | Crie a VNet Spoke para hospedar a aplicação de BI (Front-end, Middle-tier, Back-end). | Azure Virtual Network |
| **1.3** | **Peering de VNet** | Configure o **VNet Peering** entre o Hub e o Spoke, garantindo que o tráfego possa fluir entre eles de forma privada. | VNet Peering |

### Fase 2: Implementação da Conectividade Híbrida Resiliente

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **2.1** | **Conexão Primária (ExpressRoute)** | Implante um **ExpressRoute Gateway** na VNet Hub. Configure o circuito ExpressRoute para ser o caminho preferencial para o tráfego *on-premises*. | Azure ExpressRoute |
| **2.2** | **Conexão de Backup (VPN)** | Implante um **VPN Gateway** na mesma VNet Hub. Configure uma conexão VPN Site-to-Site com o *on-premises* para atuar como backup. | Azure VPN Gateway |
| **2.3** | **Failover Automático** | O roteamento BGP (Border Gateway Protocol) gerenciará o failover automático do ExpressRoute para o VPN Gateway em caso de falha do circuito primário. | BGP (Roteamento) |

### Fase 3: Segurança e Inspeção de Tráfego

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **3.1** | **Inspeção Centralizada** | Implante o **Azure Firewall** na VNet Hub. | Azure Firewall |
| **3.2** | **Forçar Roteamento (UDR)** | Crie **Rotas Definidas pelo Usuário (UDRs)** nas sub-redes do Spoke para forçar todo o tráfego (entrada, saída, leste-oeste) a passar pelo IP privado do Azure Firewall. | UDRs (User Defined Routes) |
| **3.3** | **Segmentação de Sub-redes** | Divida a VNet Spoke em sub-redes: `Subnet-Web`, `Subnet-App`, `Subnet-DB`. | Azure Virtual Network |
| **3.4** | **Controle de Tráfego (NSG/ASG)** | Crie **Application Security Groups (ASGs)** para cada camada (`ASG-Web`, `ASG-App`, `ASG-DB`). Use **NSGs** nas sub-redes para aplicar regras que referenciam os ASGs (ex: `Subnet-DB` só aceita tráfego da `ASG-App` na porta 1433). | NSG / ASG |

### Fase 4: Modernização e Resolução de Nomes

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **4.1** | **Modernização do Front-end** | Migre o Front-end (Servidores Web IIS) para o **Azure App Service** (PaaS) ou **Azure Container Apps** para melhor escalabilidade e menor custo operacional. | Azure App Service / Container Apps |
| **4.2** | **Acesso Privado à Aplicação** | Configure um **Private Endpoint** para o App Service, garantindo que o acesso à aplicação seja restrito à rede corporativa (on-premises e Azure). | Azure Private Link |
| **4.3** | **Resolução de Nomes Híbrida** | Crie uma **Azure Private DNS Zone** e configure o **Azure Private DNS Resolver** (ou servidores DNS na VNet Hub) para encaminhar consultas de nomes *on-premises* para os servidores DNS locais e vice-versa. | Azure Private DNS Zone / Resolver |

## Pilares do Well-Architected Framework (Foco na Certificação)

| Pilar | Como a Solução Aborda |
| :--- | :--- |
| **Segurança** | **Defesa em Profundidade** (Firewall, NSG/ASG, Private Link). **Zero Trust** (tráfego explicitamente permitido entre camadas). **Tráfego Privado** (ExpressRoute). |
| **Confiabilidade** | **Conectividade Resiliente** (ExpressRoute + VPN Gateway com failover). **Alta Disponibilidade** (Serviços gerenciados PaaS/Firewall com SLA elevado). |
| **Excelência Operacional** | **Gerenciamento Centralizado** (Topologia Hub-Spoke). **Modernização** (Migração para PaaS/Contêineres para reduzir a sobrecarga de gerenciamento de VMs). |

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
