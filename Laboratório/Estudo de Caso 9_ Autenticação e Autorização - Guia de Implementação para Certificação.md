# Estudo de Caso 9: Autenticação e Autorização - Guia de Implementação para Certificação

Este guia detalha o **passo a passo de implementação** para gerenciar identidades, proteger acessos e garantir a comunicação segura entre aplicações e bancos de dados no Azure, utilizando o **Microsoft Entra ID** (antigo Azure AD).

## Cenário e Requisitos Chave

O cenário envolve a integração de novos usuários, a proteção de acessos geográficos incomuns e a implementação de uma solução de acesso seguro (sem senhas) para uma nova aplicação e seu banco de dados SQL.

| Requisito | Objetivo de Implementação | Ferramenta Chave |
| :--- | :--- | :--- |
| **Gerenciamento de Novos Usuários** | Adicionar e gerenciar 75 novos funcionários de forma eficiente. | Microsoft Entra ID (Grupos de Segurança) |
| **Segurança Geográfica** | Proteger contra acessos de locais incomuns ou não confiáveis. | Acesso Condicional (Conditional Access) com MFA |
| **Acesso Seguro App -> DB** | Permitir que a aplicação acesse o banco de dados sem credenciais no código. | Identidades Gerenciadas (Managed Identities) |

## Passo a Passo de Implementação (Microsoft Entra ID e Segurança)

A solução se concentra em três pilares de identidade e acesso.

### Fase 1: Gerenciamento de Identidades e Automação

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **1.1** | **Criação de Usuários** | Crie as 75 novas contas de usuário no **Microsoft Entra ID**. | Microsoft Entra ID |
| **1.2** | **Criação de Grupo de Segurança** | Crie um **Grupo de Segurança** (ex: `FuncionariosAdquiridos`) e adicione todos os novos usuários a ele. | Microsoft Entra ID (Grupos) |
| **1.3** | **Atribuição de Licenças** | Use o Grupo de Segurança para atribuir licenças (ex: Microsoft 365) automaticamente a todos os membros. | Microsoft Entra ID (Licenciamento Baseado em Grupo) |

### Fase 2: Proteção de Acesso com Acesso Condicional

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **2.1** | **Definição de Localizações** | Defina as **Localizações Nomeadas** (ex: escritórios da empresa) como confiáveis no Entra ID. | Microsoft Entra ID (Localizações Nomeadas) |
| **2.2** | **Criação da Política de Acesso Condicional** | Crie uma política que tenha como alvo o Grupo de Segurança `FuncionariosAdquiridos`. | Acesso Condicional |
| **2.3** | **Configuração da Regra** | Configure a regra: **SE** o usuário for membro de `FuncionariosAdquiridos` **E** a localização for **Excluir** as Localizações Nomeadas (acesso de fora do escritório) **ENTÃO** **Conceder Acesso** exigindo **Multi-Factor Authentication (MFA)**. | Acesso Condicional |
| **2.4** | **Proteção Avançada** | Habilite o **Azure AD Identity Protection** para detectar riscos em tempo real (ex: vazamento de credenciais) e forçar redefinição de senha ou bloqueio. | Azure AD Identity Protection |

### Fase 3: Acesso Seguro da Aplicação ao Banco de Dados (Sem Senhas)

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **3.1** | **Habilitar Identidade Gerenciada** | No serviço que hospeda a aplicação (ex: Azure App Service, Azure Function), habilite a **Identidade Gerenciada Atribuída pelo Sistema**. | Managed Identities |
| **3.2** | **Configurar Administrador do SQL** | Configure o **Administrador do Azure AD** para o servidor do Azure SQL Database. | Azure SQL Database |
| **3.3** | **Criação de Usuário no SQL** | No banco de dados SQL, crie um usuário mapeado para a Identidade Gerenciada da aplicação (ex: `CREATE USER [AppService-BI] FROM EXTERNAL PROVIDER;`). | T-SQL |
| **3.4** | **Autorização (RBAC)** | Conceda as permissões necessárias (ex: `db_datareader`, `db_datawriter`) a esse novo usuário no banco de dados. | T-SQL |
| **3.5** | **Conexão da Aplicação** | A aplicação se conecta ao banco de dados usando a autenticação do Azure AD, sem precisar de *username* ou *password* na *connection string*. | Código da Aplicação |

## Resumo da Solução (Tabela para Certificação)

| Requisito | Solução Azure | Benefício de Segurança |
| :--- | :--- | :--- |
| **Gerenciamento de Usuários** | **Microsoft Entra ID** com **Grupos de Segurança**. | Centralização e gestão de políticas em massa. |
| **Proteção Geográfica** | **Acesso Condicional** exigindo **MFA** para acessos não confiáveis. | Defesa contra roubo de credenciais e acessos de risco. |
| **Acesso App -> DB** | **Identidades Gerenciadas** e **Autenticação do Azure AD** no SQL DB. | Elimina o risco de credenciais vazadas no código (Zero Trust). |

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
