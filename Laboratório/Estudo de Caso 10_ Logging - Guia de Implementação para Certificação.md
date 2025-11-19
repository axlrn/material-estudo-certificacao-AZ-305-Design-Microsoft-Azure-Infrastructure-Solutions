# Estudo de Caso 10: Logging - Guia de Implementação para Certificação

Este guia detalha o **passo a passo de implementação** para configurar um sistema de **Logging, Monitoramento e Análise** centralizado no Azure, utilizando o **Azure Monitor** e seus componentes para garantir a saúde e o desempenho das aplicações.

## Cenário e Requisitos Chave

O cenário exige a correta associação das ferramentas de monitoramento do Azure com seus casos de uso específicos, abrangendo desde a coleta de logs até a análise de desempenho de aplicações e a criação de relatórios visuais.

| Requisito | Objetivo de Implementação | Ferramenta Chave |
| :--- | :--- | :--- |
| **Plataforma Centralizada** | Coletar, analisar e agir sobre a telemetria de ambientes em nuvem e locais. | Azure Monitor |
| **Monitoramento de Aplicação (APM)** | Medir taxas de solicitação, tempos de resposta, falhas e experiência do usuário. | Application Insights |
| **Análise de Logs** | Editar e executar consultas de logs. | Log Analytics (KQL) |
| **Relatórios Visuais Avançados** | Criar painéis interativos e relatórios visuais. | Azure Workbooks (Pastas de Trabalho) |

## Passo a Passo de Implementação (Azure Monitor e Componentes)

A solução é baseada no ecossistema do **Azure Monitor**, que atua como a plataforma unificada de observabilidade.

### Fase 1: Configuração da Plataforma Central (Azure Monitor)

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **1.1** | **Criação do Log Analytics Workspace** | Crie um **Log Analytics Workspace** centralizado. Este é o repositório de dados onde todos os logs e métricas serão armazenados. | Azure Monitor (Log Analytics) |
| **1.2** | **Configuração de Retenção** | Defina a política de retenção de dados (ex: 90 dias) para logs e métricas, equilibrando conformidade e custo. | Log Analytics Workspace |

### Fase 2: Coleta de Logs e Métricas

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **2.1** | **Logs de Infraestrutura** | Configure as **Configurações de Diagnóstico (Diagnostic Settings)** em todos os recursos do Azure (VMs, SQL DBs, Key Vaults, etc.) para enviar seus logs e métricas para o Log Analytics Workspace. | Azure Monitor (Diagnostic Settings) |
| **2.2** | **Logs de Aplicação (APM)** | Integre o **Application Insights** ao código da aplicação. Isso coleta telemetria detalhada (taxas de solicitação, tempos de resposta, falhas, diagnósticos de transações) e a envia para o Log Analytics Workspace. | Application Insights |
| **2.3** | **Logs de Sistemas Operacionais** | Para VMs, instale o **Azure Monitor Agent (AMA)** para coletar logs de eventos do SO e logs de desempenho. | Azure Monitor Agent |

### Fase 3: Análise e Visualização

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **3.1** | **Análise de Logs** | Use o **Log Analytics** para escrever e executar consultas na **Kusto Query Language (KQL)**. KQL permite buscar, filtrar, correlacionar e agregar dados de logs de todas as fontes. | Log Analytics (KQL) |
| **3.2** | **Análise de Aplicação** | Use o **Application Insights** para visualizar o mapa da aplicação, funis de conversão e rastrear a experiência do usuário (cliente e servidor). | Application Insights |
| **3.3** | **Criação de Relatórios Visuais** | Use as **Pastas de Trabalho do Azure (Azure Workbooks)** para criar relatórios visuais avançados e interativos, combinando dados de logs (KQL) e métricas. | Azure Workbooks |

### Fase 4: Ação e Alerta

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **4.1** | **Configuração de Alertas** | Crie **Regras de Alerta** baseadas em: 1) **Métricas** (ex: CPU > 90%) e 2) **Logs** (ex: contagem de erros críticos > X em 5 minutos). | Azure Monitor (Alertas) |
| **4.2** | **Grupos de Ação** | Configure **Grupos de Ação (Action Groups)** para definir a resposta a um alerta (ex: notificação por e-mail, SMS, ou execução de um *runbook* de automação). | Azure Monitor (Action Groups) |

## Tabela Resumo para Certificação (Ferramentas e Funções)

| Ferramenta | Função Principal | Caso de Uso Chave |
| :--- | :--- | :--- |
| **Azure Monitor** | Plataforma central de observabilidade. | Coleta e roteamento de telemetria de todas as fontes. |
| **Application Insights** | Gerenciamento de Desempenho de Aplicações (APM). | Medir tempos de resposta, taxas de falha e experiência do usuário. |
| **Log Analytics** | Ambiente de consulta de logs. | Editar e executar consultas KQL para análise de logs. |
| **Azure Workbooks** | Visualização e Relatórios Avançados. | Criar painéis interativos e relatórios visuais de saúde. |
| **Kusto Query Language (KQL)** | Linguagem de consulta. | Análise e correlação de dados de logs no Log Analytics. |
