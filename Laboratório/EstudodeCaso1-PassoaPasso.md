# Estudo de Caso 1: Novo Projeto de Desenvolvimento - Guia de Implementação para Certificação

Este guia detalha o **passo a passo de implementação** para atender aos requisitos de um novo projeto de desenvolvimento, focando em **controle de custos** e **governança de recursos** na plataforma Azure.

## Cenário e Requisitos Chave

| Requisito | Objetivo de Implementação |
| :--- | :--- |
| **Capturar todos os custos** | Isolar e rastrear custos de forma granular. |
| **Cargas de trabalho de teste em VMs de baixo custo** | Restringir o uso de máquinas virtuais (VMs) a *SKUs* (tamanhos) econômicos. |
| **Nomeação padrão para VMs** | Impor um padrão de nomenclatura para fácil identificação e organização. |
| **Identificar não conformidade** | Bloquear ativamente a criação de recursos que violem as regras (abordagem de Imposição/Deny). |

## Passo a Passo de Implementação (Azure)

A solução proposta utiliza **Grupos de Recursos**, **Tags** e **Azure Policy** para garantir a conformidade e o controle de custos.

### Fase 1: Isolamento e Rastreamento de Custos

O primeiro passo é garantir que todos os custos do projeto sejam facilmente rastreáveis e isolados de outros projetos.

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **1.1** | **Criação do Grupo de Recursos (Resource Group - RG)** | Crie um RG dedicado para o projeto. Todos os recursos (VMs, redes, etc.) serão implantados neste grupo. | Azure Portal / Azure CLI / Azure PowerShell |
| **1.2** | **Definição de Tags de Custo e Organização** | Defina um conjunto de *tags* (etiquetas) obrigatórias para rastreamento de custos e organização. | Azure Policy (para imposição) |
| **1.3** | **Aplicação de Tags** | Aplique as *tags* no nível do Grupo de Recursos para que sejam herdadas pelos recursos, ou use o Azure Policy para exigir que elas sejam aplicadas a cada recurso. | Azure Portal / Azure CLI / Azure PowerShell |
| **1.4** | **Monitoramento de Custos** | Utilize o serviço para visualizar e filtrar os custos agregados pelo Grupo de Recursos e pelas *tags* definidas. | Azure Cost Management + Billing |

**Exemplo de Tags Essenciais:**
*   `Projeto`: `FeedbackClientes`
*   `CentroDeCusto`: `CC12345`
*   `Ambiente`: `Desenvolvimento`

### Fase 2: Imposição de Governança (Azure Policy)

Para garantir que as regras de nomenclatura e dimensionamento sejam seguidas, utilizaremos o **Azure Policy** com o efeito **Deny** (Imposição).

#### 2.1. Política de Nomenclatura (Naming Convention)

| Etapa | Ação | Detalhes da Implementação | Efeito da Política |
| :--- | :--- | :--- | :--- |
| **2.1.1** | **Criação da Definição de Política** | Crie uma política que exija que todos os recursos do tipo `Microsoft.Compute/virtualMachines` (VMs) no escopo do RG sigam um padrão de nomenclatura específico (ex: `proj-feedback-vm-*`). | `Deny` |
| **2.1.2** | **Atribuição da Política** | Atribua a política de nomenclatura ao **Grupo de Recursos** criado na Fase 1. | `Deny` |
| **2.1.3** | **Teste** | Tente criar uma VM com um nome que não siga o padrão. A operação deve ser **bloqueada** imediatamente. | |

#### 2.2. Política de Dimensionamento (Restrição de SKU/Custo)

| Etapa | Ação | Detalhes da Implementação | Efeito da Política |
| :--- | :--- | :--- | :--- |
| **2.2.1** | **Criação da Definição de Política** | Crie uma política que restrinja os *SKUs* (tamanhos) de VMs permitidos. Permita apenas *SKUs* de baixo custo (ex: séries B ou Dsv3 de tamanhos menores) e proíba *SKUs* caros (ex: séries E ou M). | `Deny` |
| **2.2.2** | **Atribuição da Política** | Atribua a política de dimensionamento ao **Grupo de Recursos**. | `Deny` |
| **2.2.3** | **Teste** | Tente criar uma VM com um *SKU* proibido. A operação deve ser **bloqueada** imediatamente. | |

### Resumo da Decisão (Justificativa para Certificação)

A escolha da abordagem de **Imposição (Deny)** no Azure Policy é a mais robusta para atender ao requisito de **"Identificar não conformidade"**.

*   **Prevenção vs. Correção:** O efeito `Deny` **previne** a criação de recursos não conformes, garantindo o controle de custos e a organização desde o início.
*   **Conformidade Automática:** Elimina a necessidade de monitoramento manual e correção de problemas, o que seria necessário com o efeito `Audit`.
*   **Controle de Custos:** Ao bloquear *SKUs* caros, a política garante que o requisito de "VMs de baixo custo" seja cumprido, evitando surpresas na fatura.

---

# Estudo de Caso 2: Custo e Contabilidade - Guia de Implementação para Certificação

## Cenário e Requisitos Chave

O Estudo de Caso 2 foca em **otimização de custos** e **visibilidade contábil** em um ambiente de nuvem existente.

| Requisito | Objetivo de Implementação |
| :--- | :--- |
| **Otimização de Custos** | Identificar e reduzir gastos desnecessários. |
| **Visibilidade Contábil** | Atribuir custos de nuvem a departamentos e centros de custo específicos. |
| **Gerenciamento de Orçamento** | Estabelecer limites de gastos e alertas. |

## Passo a Passo de Implementação (Azure)

A solução se concentra em **Azure Cost Management + Billing**, **Tags** e **Azure Advisor**.

### Fase 1: Análise e Otimização de Custos

O primeiro passo é entender onde o dinheiro está sendo gasto e identificar oportunidades de economia.

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **1.1** | **Revisão de Custos Atuais** | Analise o relatório de custos para identificar os serviços mais caros e os picos de uso. | Azure Cost Management |
| **1.2** | **Recomendações de Otimização** | Verifique as recomendações de otimização de custos, como VMs subutilizadas, discos não anexados ou recursos ociosos. | Azure Advisor (seção Custo) |
| **1.3** | **Implementação de Economias** | Redimensione (downsize) VMs subutilizadas, exclua recursos ociosos e considere o uso de **Instâncias Reservadas (Reserved Instances - RIs)** ou **Azure Hybrid Benefit** para cargas de trabalho estáveis. | Azure Portal / Azure CLI |

### Fase 2: Visibilidade Contábil e Alocação de Custos

Para atribuir custos a departamentos e centros de custo, a marcação (tagging) é essencial.

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **2.1** | **Definição de Tags de Alocação** | Padronize as *tags* para alocação de custos (ex: `Departamento`, `CentroDeCusto`). | Documentação Interna / Azure Policy |
| **2.2** | **Aplicação de Tags em Massa** | Aplique as *tags* definidas a todos os recursos existentes, usando scripts ou ferramentas de gerenciamento de recursos. | Azure CLI / Azure PowerShell / Azure Resource Graph |
| **2.3** | **Relatórios de Alocação** | Crie relatórios personalizados no Cost Management, agrupando os custos pelas *tags* de `Departamento` e `CentroDeCusto`. | Azure Cost Management |

### Fase 3: Gerenciamento de Orçamento e Alertas

Para evitar gastos excessivos, é crucial estabelecer orçamentos e alertas.

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **3.1** | **Criação de Orçamento** | Defina um orçamento mensal ou trimestral para a Assinatura ou para Grupos de Recursos específicos. | Azure Cost Management |
| **3.2** | **Configuração de Alertas** | Configure alertas para serem disparados em diferentes limiares (ex: 50%, 75%, 90% do orçamento). Os alertas devem notificar os responsáveis (ex: Gerente de Projeto, CFO). | Azure Cost Management (Alertas de Orçamento) |
| **3.3** | **Ação Automatizada (Opcional)** | Use Grupos de Ação (Action Groups) para integrar os alertas com automação (ex: rodar um *runbook* para desligar VMs de desenvolvimento ao atingir 90% do orçamento). | Azure Cost Management + Action Groups + Azure Automation |

---

# Estudo de Caso 3: Simples - Lançamento de Novo E-commerce - Guia de Implementação para Certificação

## Cenário e Requisitos Chave

O Estudo de Caso 3 trata do lançamento de um novo site de e-commerce, focando em **escalabilidade**, **alta disponibilidade** e **entrega de conteúdo global**.

| Requisito | Objetivo de Implementação |
| :--- | :--- |
| **Escalabilidade e Alta Disponibilidade** | A aplicação deve lidar com picos de tráfego e estar sempre online. |
| **Entrega de Conteúdo Rápida** | O conteúdo estático (imagens, CSS, JS) deve ser entregue rapidamente aos usuários em qualquer lugar do mundo. |
| **Ambiente de Desenvolvimento/Teste** | Necessidade de um ambiente de *staging* para testes antes da produção. |

## Passo a Passo de Implementação (Azure)

A solução utiliza **Azure App Service** para a aplicação web e **Azure Content Delivery Network (CDN)** para o conteúdo estático.

### Fase 1: Configuração da Aplicação Web (Back-end e Front-end Dinâmico)

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **1.1** | **Escolha do Serviço de Hospedagem** | Selecione o **Azure App Service** (Plano *Standard* ou superior) para hospedar a aplicação de e-commerce. | Azure App Service |
| **1.2** | **Configuração de Slots de Implantação** | Crie um *Deployment Slot* (Slot de Implantação) chamado `staging` (teste) e mantenha o *slot* `production` (produção). Isso permite testes sem afetar o ambiente ativo. | Azure App Service Deployment Slots |
| **1.3** | **Configuração de Auto-Escalabilidade** | Configure o *Autoscale* (Escalabilidade Automática) para adicionar instâncias da aplicação durante picos de tráfego (ex: CPU > 70%) e remover instâncias quando o tráfego diminuir. | Azure App Service Autoscale |
| **1.4** | **Implantação** | Implante o código da aplicação no *slot* `staging` para testes. Após a validação, use a função **Swap** para trocar o `staging` pelo `production` (troca instantânea de tráfego). | Azure DevOps / GitHub Actions / Azure Portal |

### Fase 2: Otimização da Entrega de Conteúdo Estático

Para garantir a velocidade global, o conteúdo estático deve ser servido a partir de pontos de presença (PoPs) próximos aos usuários.

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **2.1** | **Hospedagem de Conteúdo Estático** | Mova todo o conteúdo estático (imagens de produtos, CSS, JS) para um **Azure Storage Account** (Blob Storage). | Azure Blob Storage |
| **2.2** | **Criação da CDN** | Crie um perfil de **Azure Content Delivery Network (CDN)**. | Azure CDN |
| **2.3** | **Configuração do *Endpoint*** | Crie um *endpoint* na CDN e defina o **Blob Storage** (do passo 2.1) como a origem (*origin*). | Azure CDN |
| **2.4** | **Integração** | Atualize o código do e-commerce para que os links para o conteúdo estático apontem para o domínio da CDN (ex: `https://meucdn.azureedge.net/images/produto.jpg`). | Código da Aplicação |

### Fase 3: Segurança e Domínio

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **3.1** | **Certificado SSL/TLS** | Garanta que o certificado SSL/TLS esteja configurado e ativo no App Service e na CDN. | Azure App Service / Azure CDN |
| **3.2** | **Mapeamento de Domínio Personalizado** | Mapeie o domínio do e-commerce (ex: `www.meuecommerce.com.br`) para o **Azure App Service** e/ou para o **Azure CDN** (se o domínio for usado para a CDN). | Azure DNS / DNS do Provedor |

---

# Estudo de Caso 4: Dados Não Relacionais - Guia de Implementação para Certificação

## Cenário e Requisitos Chave

O Estudo de Caso 4 aborda a necessidade de um banco de dados que lide com grandes volumes de dados, alta taxa de transferência e um esquema flexível (dados não relacionais/NoSQL).

| Requisito | Objetivo de Implementação |
| :--- | :--- |
| **Esquema Flexível** | O banco de dados deve suportar dados que mudam de estrutura frequentemente (ex: catálogos de produtos com atributos variáveis). |
| **Escalabilidade Global e Alta Disponibilidade** | Capacidade de escalar horizontalmente e replicar dados em múltiplas regiões. |
| **Baixa Latência** | Acesso rápido aos dados, essencial para aplicações de alto desempenho. |

## Passo a Passo de Implementação (Azure)

A solução ideal para este cenário é o **Azure Cosmos DB**, um serviço de banco de dados NoSQL distribuído globalmente.

### Fase 1: Configuração Inicial do Cosmos DB

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **1.1** | **Criação da Conta Cosmos DB** | Crie uma conta do Cosmos DB, escolhendo a API mais adequada (ex: API Core (SQL) para flexibilidade e recursos nativos). | Azure Cosmos DB |
| **1.2** | **Configuração de Distribuição Global** | Habilite a replicação de dados em múltiplas regiões geográficas (ex: Leste dos EUA e Sul do Brasil) para alta disponibilidade e baixa latência global. | Azure Cosmos DB (Replicar dados globalmente) |
| **1.3** | **Definição da Chave de Partição** | Escolha uma **Chave de Partição** (Partition Key) que distribua uniformemente a carga de trabalho e o armazenamento (ex: `idDoProduto` ou `idDoCliente`). Uma chave mal escolhida pode levar a *hot partitions*. | Azure Cosmos DB (Contêiner) |

### Fase 2: Modelagem e Ingestão de Dados

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **2.1** | **Criação do Banco de Dados e Contêiner** | Crie um banco de dados e, dentro dele, um contêiner (coleção) para armazenar os documentos (dados). | Azure Cosmos DB |
| **2.2** | **Configuração de *Throughput*** | Defina o *throughput* (vazão) em **Unidades de Requisição (Request Units - RUs)**. Comece com um valor estimado e use o *Autoscale* para ajustar dinamicamente. | Azure Cosmos DB (Contêiner) |
| **2.3** | **Modelagem de Documentos** | Modele os dados como documentos JSON, aproveitando a flexibilidade do esquema. **Embuta** dados relacionados (ex: avaliações de produtos dentro do documento do produto) para reduzir consultas e latência. | Código da Aplicação / Cosmos DB Data Explorer |

### Fase 3: Otimização de Desempenho

| Etapa | Ação | Detalhes da Implementação | Ferramenta/Serviço |
| :--- | :--- | :--- | :--- |
| **3.1** | **Indexação** | Revise a política de indexação. O Cosmos DB indexa tudo por padrão, mas você pode otimizar (excluir caminhos não utilizados) para reduzir o custo de RUs de escrita. | Azure Cosmos DB (Política de Indexação) |
| **3.2** | **Consistência** | Escolha o nível de consistência apropriado. Para e-commerce, **Sessão** ou **Bouded Staleness** são bons compromissos entre latência e consistência. | Azure Cosmos DB (Nível de Consistência) |
| **3.3** | **Monitoramento** | Monitore o consumo de RUs e a latência para garantir que o desempenho atenda aos requisitos de baixa latência. | Azure Monitor / Cosmos DB Metrics |

---

# Estudo de Caso 5: Dados Relacionais - Guia de Implementação para Certificação

## Cenário e Requisitos Chave

O Estudo de Caso 5 exige um banco de dados que lide com **transações complexas**, **integridade referencial** e **consultas SQL** tradicionais (dados relacionais).

| Requisito | Objetivo de Implementação |
| :--- | :--- |
| **Transações ACID** | Garantir a atomicidade, consistência, isolamento e durabilidade das transações (ex: pedidos de clientes, inventário). |
| **Integridade de Dados** | Manter a integridade referencial através de chaves primárias e estrangeiras. |
| **Alta Disponibilidade e Recuperação de Desastres** | O banco de dados deve ser resiliente a falhas. |

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

| Requisito | Objetivo de Implementação |
| :--- | :--- |
| **Mensageria Confiável** | Garantir que as mensagens de pedido e estoque sejam entregues e processadas, mesmo sob falha. |
| **Processamento Assíncrono** | Desacoplar o sistema de pedidos do sistema de estoque para que um não afete a disponibilidade do outro. |
| **Escalabilidade de Eventos** | Lidar com um grande volume de eventos de estoque e pedidos. |

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

| Requisito | Objetivo de Implementação |
| :--- | :--- |
| **Microserviços** | Dividir a aplicação em serviços menores e independentes. |
| **Conteneirização** | Empacotar cada serviço com suas dependências para portabilidade. |
| **Orquestração** | Gerenciar o ciclo de vida, escalabilidade e rede dos contêineres. |
| **CI/CD** | Implementar um pipeline de integração e entrega contínua. |

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

| Requisito | Objetivo de Implementação |
| :--- | :--- |
| **Conectividade Híbrida** | Estabelecer uma conexão segura e privada entre a rede *on-premises* e a nuvem. |
| **Migração de Aplicação** | Mover a aplicação interna para um ambiente de nuvem moderno. |
| **Modernização** | Conteneirizar a aplicação para maior agilidade. |

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

| Requisito | Objetivo de Implementação |
| :--- | :--- |
| **Identidade Centralizada** | Usar um provedor de identidade único para todos os usuários. |
| **Autenticação Segura** | Implementar padrões modernos (ex: OAuth 2.0, OpenID Connect). |
| **Controle de Acesso Baseado em Função (RBAC)** | Definir permissões com base no papel do usuário. |

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

| Requisito | Objetivo de Implementação |
| :--- | :--- |
| **Centralização de Logs** | Coletar logs de todas as fontes (aplicação, infraestrutura, segurança) em um único local. |
| **Análise e Busca** | Capacidade de buscar, filtrar e analisar logs rapidamente. |
| **Monitoramento e Alerta** | Criar painéis de monitoramento e alertas proativos. |

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
