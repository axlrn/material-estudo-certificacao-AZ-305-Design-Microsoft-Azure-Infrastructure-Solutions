# Estudo de Caso 4: Dados Não Relacionais - Guia de Implementação para Certificação

Este guia detalha o **passo a passo de implementação** para projetar uma solução de **armazenamento de dados não relacionais (Blob Storage)** no Azure, atendendo a requisitos distintos de acesso, segurança e custo.

## Cenário e Requisitos Chave

O cenário exige o armazenamento de três tipos de dados (Mídia, Marketing, Corporativos) com diferentes necessidades de acesso (público vs. privado) e desempenho.

| Tipo de Dado | Requisito de Acesso | Requisito de Desempenho/Custo |
| :--- | :--- | :--- |
| **Mídia (Imagens/Vídeos)** | Público (acessível pelo site). | Baixa latência global (CDN). |
| **Literatura de Marketing (PDFs)** | Público (acessível pelo site). | Baixa latência global (CDN). |
| **Documentos Corporativos** | Privado (apenas funcionários internos). | Alta segurança, otimização de custo para dados antigos. |

## Passo a Passo de Implementação (Azure Blob Storage)

A solução se baseia no **Azure Blob Storage** e na aplicação de diferentes políticas de segurança e otimização para cada tipo de dado.

### Fase 1: Configuração da Conta de Armazenamento

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **1.1** | **Criação da Conta** | Crie uma única **Azure Storage Account** (tipo *Standard General-purpose v2*). | Azure Storage Account |
| **1.2** | **Criação de Contêineres** | Crie três contêineres distintos para isolamento lógico e aplicação de políticas: `media`, `marketing` e `corporate`. | Azure Blob Storage |
| **1.3** | **Configuração de Acesso Público** | Defina o nível de acesso para os contêineres `media` e `marketing` como **Público Anônimo** (Blob ou Contêiner). | Azure Blob Storage |
| **1.4** | **Configuração de Acesso Privado** | Mantenha o nível de acesso para o contêiner `corporate` como **Privado** (sem acesso anônimo). | Azure Blob Storage |

### Fase 2: Otimização de Desempenho e Custo (Camadas de Acesso)

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **2.1** | **Camada Hot (Mídia e Marketing)** | Defina a camada de acesso padrão para os contêineres `media` e `marketing` como **Hot** (Quente), pois o acesso é frequente e o desempenho é crítico. | Azure Blob Storage (Access Tier) |
| **2.2** | **Otimização de Custo (Corporate)** | Para o contêiner `corporate`, implemente uma **Política de Gerenciamento do Ciclo de Vida (Lifecycle Management)**. | Azure Lifecycle Management |
| **2.3** | **Regra de Ciclo de Vida** | Crie uma regra para mover blobs no contêiner `corporate` da camada **Hot** para **Cool** se não forem acessados por 90 dias, e para **Archive** se não forem acessados por 365 dias. | Azure Lifecycle Management |
| **2.4** | **Aceleração de Conteúdo Público** | Crie um **Azure Content Delivery Network (CDN)** e configure-o para usar os contêineres `media` e `marketing` como origem. Isso garante baixa latência global. | Azure CDN |

### Fase 3: Implementação de Segurança para Dados Corporativos (Privados)

A segurança do contêiner `corporate` exige uma abordagem em camadas.

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **3.1** | **Isolamento de Rede** | Crie um **Ponto de Extremidade Privado (Private Endpoint)** para a Conta de Armazenamento. Isso remove o acesso à conta da internet pública e a integra à **Rede Virtual (VNet)** interna da empresa. | Azure Private Link / VNet |
| **3.2** | **Autenticação** | Exija que os funcionários usem suas credenciais do **Microsoft Entra ID** (Azure AD) para acessar o armazenamento. | Microsoft Entra ID |
| **3.3** | **Autorização (RBAC)** | Use o **Controle de Acesso Baseado em Função (RBAC)** para conceder permissões granulares (ex: `Storage Blob Data Reader`, `Storage Blob Data Contributor`) a grupos específicos do Entra ID (ex: Grupo RH, Grupo Finanças) apenas no contêiner `corporate`. | Azure RBAC |
| **3.4** | **Imutabilidade (Opcional)** | Se houver requisitos de conformidade (ex: Sarbanes-Oxley), configure o **Armazenamento Imutável (Immutable Storage)** no contêiner `corporate` para impor uma política WORM (*Write-Once, Read-Many*). | Azure Blob Storage (Immutability) |

## Resumo da Solução Final (Tabela para Certificação)

| Categoria de Dados | Contêiner | Camada de Acesso | Mecanismo de Acesso e Segurança |
| :--- | :--- | :--- | :--- |
| **Mídia e Marketing** | `media`, `marketing` | Hot | **Acesso Público Anônimo** + **Azure CDN** (para desempenho). |
| **Documentos Corporativos** | `corporate` | Hot/Cool/Archive | **Private Endpoint** (isolamento de rede) + **Microsoft Entra ID** (autenticação) + **Azure RBAC** (autorização). |

---

# Estudo de Caso 5: Dados Relacionais - Guia de Implementação para Certificação

## Cenário e Requisitos Chave

O Estudo de Caso 5 exige um banco de dados que lide com **transações complexas**, **integridade referencial** e **consultas SQL** tradicionais (dados relacionais).

| Requisito | Objetivo de Implementação | Ferramenta Chave |
| :--- | :--- | :--- |
| **Transações ACID** | Garantir a atomicidade, consistência, isolamento e durabilidade das transações. | Azure SQL Database |
| **Integridade de Dados** | Manter a integridade referencial através de chaves primárias e estrangeiras. | Azure SQL Database |
| **Alta Disponibilidade e Recuperação de Desastres** | O banco de dados deve ser resiliente a falhas. | Azure SQL Database (Failover Groups) |

## Passo a Passo de Implementação (Azure)

A solução ideal é o **Azure SQL Database** ou **Azure Database for MySQL/PostgreSQL**, dependendo da preferência do motor. Usaremos o Azure SQL Database como exemplo.

### Fase 1: Configuração do Banco de Dados

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **1.1** | **Criação do Azure SQL Database** | Crie um novo banco de dados SQL. Escolha o modelo de compra (vCore ou DTU) e o nível de serviço (ex: *General Purpose* para a maioria das cargas de trabalho). | Azure SQL Database |
| **1.2** | **Configuração de Firewall** | Configure as regras de firewall para permitir o acesso apenas a partir da aplicação (ex: IP do App Service) e da rede de administração. | Azure SQL Database (Firewall e Redes Virtuais) |
| **1.3** | **Segurança de Conexão** | Use **Azure Key Vault** para armazenar a *string* de conexão e as credenciais do banco de dados, e configure a aplicação para acessá-las via **Identidades Gerenciadas (Managed Identities)**. | Azure Key Vault / Azure App Service |

### Fase 2: Alta Disponibilidade e Recuperação de Desastres

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **2.1** | **Alta Disponibilidade (HA)** | O Azure SQL Database já inclui HA nativa. Para maior resiliência, escolha o nível de serviço **Business Critical** (que usa *Always On Availability Groups*). | Azure SQL Database (Nível de Serviço) |
| **2.2** | **Backups** | Verifique a política de retenção de backups automáticos e configure backups de longo prazo (Long-Term Retention - LTR) se necessário. | Azure SQL Database (Backups) |
| **2.3** | **Recuperação de Desastres (DR)** | Configure um **Grupo de Failover Automático (Auto-Failover Group)** para replicar o banco de dados para uma região secundária, permitindo um *failover* rápido em caso de desastre regional. | Azure SQL Database (Failover Groups) |

### Fase 3: Otimização de Desempenho e Manutenção

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **3.1** | **Otimização de Consultas** | Use o **Query Performance Insight** para identificar consultas lentas e otimizar índices. | Azure SQL Database (Query Performance Insight) |
| **3.2** | **Ajuste Automático** | Habilite o **Automatic Tuning** para que o Azure SQL Database gerencie automaticamente a criação de índices e a correção de planos de execução. | Azure SQL Database (Automatic Tuning) |
| **3.3** | **Monitoramento** | Monitore o uso de CPU, I/O e memória para garantir que o nível de serviço escolhido seja adequado para a carga de trabalho. | Azure Monitor / Azure SQL Database Metrics |

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
