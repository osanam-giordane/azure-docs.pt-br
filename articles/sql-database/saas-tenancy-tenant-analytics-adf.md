---
title: Executar consultas de análise em bancos de dados de locatário usando o SQL Data Warehouse do Azure | Microsoft Docs
description: Consultas de análise entre locatários usando dados extraídos de vários bancos de dados do Banco de Dados Azure SQL.
keywords: tutorial do banco de dados SQL
services: sql-database
documentationcenter: ''
author: anumjs
manager: craigg
editor: MightyPen
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/08/2017
ms.author: anjangsh
ms.openlocfilehash: c7580e5481288695d3b5dea8fd0547f5f2c4c2b0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34643994"
---
# <a name="explore-saas-analytics-with-azure-sql-database-sql-data-warehouse-data-factory-and-power-bi"></a>Explore a análise de SaaS com o Banco de Dados SQL do Azure, o SQL Data Warehouse, o Data Factory e o Power BI

Neste tutorial, você percorre um cenário de análise de ponta a ponta. O cenário demonstra como a análise de dados de locatário pode capacitar fornecedores de software a tomarem decisões inteligentes. Usando dados extraídos de cada banco de dados de locatário, você usa a análise para obter informações sobre o comportamento de locatários, incluindo o uso do aplicativo de exemplo Wingtip Tickets SaaS. Este cenário envolve três etapas: 

1.  **Extrair dados** de cada banco de dados de locatário para um repositório de análise, neste caso, um SQL Data Warehouse.
2.  **Otimize os dados extraídos** para processamento de análise.
3.  Use ferramentas de **Business Intelligence** para obter percepções úteis, que podem orientar a tomada de decisões. 

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> - Criar o repositório de análise de locatário para carregamento.
> - Usar o ADF (Azure Data Factory) para extrair dados de cada banco de dados de locatário para o data warehouse de análise.
> - Otimize os dados extraídos (reorganize em um esquema de estrela).
> - Consultar o data warehouse de análise.
> - Use o Power BI para visualização de dados para realçar as tendências dos dados de locatário e fazer recomendações para melhorias.

![architectureOverView](media/saas-tenancy-tenant-analytics/adf_overview.png)

## <a name="analytics-over-extracted-tenant-data"></a>Análise de dados de locatário extraídos

Aplicativos SaaS mantêm uma quantidade potencialmente grande de dados de locatário na nuvem. Esses dados podem ser uma rica fonte de informações sobre a operação e o uso do aplicativo, bem como o comportamento dos locatários. Esses insights podem orientar esforços de desenvolvimento de recursos, aprimoramentos de usabilidade e outros investimentos nos aplicativos e na plataforma.

Acessar os dados para todos os locatários é simples quando todos os dados estão em apenas um banco de dados multilocatário. No entanto, o acesso é mais complexo quando distribuído em grande escala entre milhares de bancos de dados. Uma maneira de controlar a complexidade é extrair os dados para um banco de dados de análise ou para um data warehouse para fazer consultas.

Este tutorial apresenta um cenário de análise de ponta a ponta do aplicativo Wingtip Tickets. Primeiro, o [ADF (Azure Data Factory)](../data-factory/introduction.md) é usado como ferramenta de orquestração para extrair dados de vendas de ingressos e dados relacionados de cada banco de dados de locatário. Esses dados são carregados em tabelas de preparo em um repositório de análise. O repositório de análise pode ser um Banco de Dados SQL ou um SQL Data Warehouse. Este tutorial usa o [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) como repositório de análise.

Em seguida, os dados extraídos são transformados e carregados em um conjunto de tabelas de [esquema em estrela](https://www.wikipedia.org/wiki/Star_schema). As tabelas consistem em uma tabela de fatos central, mais tabelas de dimensões relacionadas:

- A tabela de fatos central no esquema de estrela contém dados de tíquetes.
- As tabelas de dimensões contêm dados sobre locais, eventos, clientes e datas de compra.

Juntas, as tabelas central e de dimensão habilitam o processamento analítico eficiente. O esquema em estrela usado neste tutorial é exibido na seguinte imagem:
 
![architectureOverView](media/saas-tenancy-tenant-analytics/starschematables.JPG)

Por fim, as tabelas de esquema em estrela são consultadas. Os resultados da consulta são exibidos visualmente usando o Power BI para realçar os insights sobre o comportamento do locatário e o uso do aplicativo. Com esse esquema em estrela, você executa consultas que expõem:

- Quem está comprando ingressos e de qual local.
- Padrões e tendências de venda de ingressos.
- A popularidade relativa de cada local.

Este tutorial fornece exemplos básicos de insights que podem ser obtidos dos dados do Wingtip Tickets. Entender como cada casa de show usa o serviço pode levar o fornecedor do Wingtip Tickets a pensar em diferentes planos de serviço direcionados a casas de show mais ou menos ativas, por exemplo. 

## <a name="setup"></a>Configuração

### <a name="prerequisites"></a>pré-requisitos

> [!NOTE]
> Este tutorial usa recursos do Azure Data Factory que estão na fase de versão prévia limitada (parametrização de serviço vinculado). Se quiser fazer este tutorial, forneça sua ID de assinatura [aqui](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxrVywox1_tHk9wgd5P8SVJUNlFINjNEOElTVFdMUEREMjVVUlJCUDdIRyQlQCN0PWcu). Nós enviaremos uma confirmação assim que sua assinatura for habilitada.

Para concluir este tutorial, certifique-se de atender a todos os seguintes pré-requisitos:
- O aplicativo Wingtip Tickets SaaS Database Per Tenant é implantado. Para implantar em menos de cinco minutos, confira [Implantar e explorar o aplicativo de SaaS do Wingtip](saas-dbpertenant-get-started-deploy.md).
- Os scripts Wingtip Tickets SaaS Database Per Tenant e o [código-fonte](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) do aplicativo são baixadas do GitHub. Veja as instruções de download. Não se esqueça de *desbloquear o arquivo zip* antes de extrair seu conteúdo.
- O Power BI Desktop está instalado. [Baixar o Power BI Desktop](https://powerbi.microsoft.com/downloads/).
- O lote de locatários adicionais foi provisionado. Confira o [**Tutorial de provisionamento de locatários**](saas-dbpertenant-provision-and-catalog.md).

### <a name="create-data-for-the-demo"></a>Criar dados para a demonstração

Este tutorial explora a análise de dados de vendas de ingressos. Nesta etapa, você gera dados de ingressos para todos os locatários. Posteriormente, esses dados serão extraídos para análise. _Certifique-se de ter provisionado o lote de locatários_ (conforme descrito anteriormente) para que você tenha dados suficientes para expor uma variedade de padrões diferentes de compras de ingressos.

1. No ISE do PowerShell, abra *…\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1* e defina o seguinte valor:
    - **$DemoScenario** = **1** Comprar ingressos para eventos em todos os locais
2. Pressione **F5** para executar o script e criar o histórico de compras de ingressos de todas as casas de show. Com 20 locatários, o script gera dezenas de milhares de ingressos e pode levar 10 minutos ou mais para ser executado.

### <a name="deploy-sql-data-warehouse-data-factory-and-blob-storage"></a>Implantar o SQL Data Warehouse, o Data Factory e o Armazenamento de Blobs 
No aplicativo Wingtip Tickets, os dados transacionais dos locatários são distribuídos entre vários bancos de dados. O ADF (Azure Data Factory) é usado para orquestrar o processo de ELT (extração, carregamento e transformação) desses dados no data warehouse. Para carregar dados no SQL Data Warehouse com mais eficiência, o ADF extrai dados em arquivos de blob intermediários e, em seguida, usa o [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading) para carregar os dados no data warehouse.   

Nesta etapa, você implanta os recursos adicionais usados no tutorial: um SQL Data Warehouse chamado _tenantanalytics_, um Azure Data Factory chamado _dbtodwload-\<usuário\>_ e uma conta de armazenamento do Azure chamada _wingtipstaging\<usuário\>_. A conta de armazenamento é usada para armazenar temporariamente os arquivos de dados extraídos como blobs antes que eles sejam carregados no data warehouse. Essa etapa também implanta o esquema do data warehouse e define os pipelines do ADF que orquestram o processo de ELT.
1. No ISE do PowerShell, abra *…\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1* e defina:
    - **$DemoScenario** = **2** Implantar data warehouse, armazenamento de blob e data factory de análise de locatário 
1. Pressione **F5** para executar o script de demonstração e implantar os recursos do Azure. 

Agora, examine os recursos do Azure implantados:
#### <a name="tenant-databases-and-analytics-store"></a>Bancos de dados de locatário e repositório de análise
Use o [SSMS (SQL Server Management Studio)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) para se conectar aos servidores **tenants1-dpt-&lt;usuário&gt;** e **catalog-dpt-&lt;usuário&gt;**. Substitua &lt;usuário&gt; pelo valor usado quando você implantou o aplicativo. Use logon = *developer* e senha = *P@ssword1*. Veja o [tutorial introdutório](saas-dbpertenant-wingtip-app-overview.md) para obter instruções.

![Conectar-se ao servidor de Banco de Dados SQL do SSMS](media/saas-tenancy-tenant-analytics/ssmsSignIn.JPG)

No Pesquisador de Objetos:

1. Expanda o servidor *tenants1-dpt-&lt;usuário&gt;*.
1. Expanda o nó Bancos de dados e veja a lista de bancos de dados de locatário.
1. Expanda o servidor *catalog-dpt-&lt;usuário&gt;*.
1. Verifique se você vê o repositório de análise que contém os seguintes objetos:
    1. As tabelas **raw_Tickets**, **raw_Customers**, **raw_Events** e **raw_Venues** contêm dados extraídos brutos dos bancos de dados de locatário.
    1. As tabelas de esquema em estrela são **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events** e **dim_Dates** .
    1. O procedimento armazenado **sp_transformExtractedData** é usado para transformar os dados e carregá-lo nas tabelas de esquema em estrela.

![DWtables](media/saas-tenancy-tenant-analytics/DWtables.JPG)

#### <a name="blob-storage"></a>Armazenamento de blob
1. No [portal do Azure](https://ms.portal.azure.com), navegue até o grupo de recursos que você usou para implantar o aplicativo. Verifique se uma conta de armazenamento chamada **wingtipstaging\<usuário\>** foi adicionada.

  ![DWtables](media/saas-tenancy-tenant-analytics/adf-staging-storage.PNG)

1. Clique na conta de armazenamento **wingtipstaging\<usuário\>** para explorar os objetos presentes.
1. Clique no bloco **Blobs**
1. Clique no contêiner **configfile**
1. Verifique se **configfile** contém um arquivo JSON chamado **TableConfig.json**. Esse arquivo contém os nomes de tabela, nomes de coluna e nomes de coluna de rastreador de origem e de destino.

#### <a name="azure-data-factory-adf"></a>ADF (Azure Data Factory)
No grupo de recursos no [portal do Azure](https://ms.portal.azure.com), verifique se um Azure Data Factory chamado _dbtodwload-\<usuário\>_ foi adicionado. 

 ![adf_portal](media/saas-tenancy-tenant-analytics/adf-data-factory-portal.png)

Esta seção explora o data factory criado. Siga as etapas abaixo para iniciar o data factory:
1. No portal, clique no data factory chamado **dbtodwload-\<usuário\>**.
2. Clique no bloco **Criar e Monitorar** para iniciar o designer do Azure Data Factory em uma guia separada. 

## <a name="extract-load-and-transform-data"></a>Extrair, carregar e transformar dados
O Azure Data Factory é usado para orquestrar a extração, o carregamento e a transformação de dados. Neste tutorial, você extrai dados de quatro exibições SQL diferentes de cada um dos bancos de dados de locatário: **rawTickets**, **rawCustomers**, **rawEvents** e **rawVenues**. Essas exibições incluem a ID da casa de show, para que você possa distinguir os dados de cada casa de show no data warehouse. Os dados são carregados nas tabelas de preparo correspondentes no data warehouse: **raw_Tickets**, **raw_customers**, **raw_Events** e **raw_Venue**. Em seguida, um procedimento armazenado transforma os dados brutos e preenche as tabelas de esquema em estrela: **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events** e **dim_Dates**.

Na seção anterior, você implantou e inicializou os recursos necessários do Azure, incluindo o data factory. O data factory implantado inclui os pipelines, conjuntos de dados, serviços vinculados, entre outros, necessários para extrair, carregar e transformar os dados de locatário. Vamos explorar mais esses objetos e, em seguida, acionar o pipeline para mover dados dos bancos de dados de locatário para o data warehouse.

### <a name="data-factory-pipeline-overview"></a>Visão geral do pipeline do data factory
Esta seção explora os objetos criados no data factory. A figura a seguir descreve o fluxo de trabalho geral do pipeline do ADF usado neste tutorial. Se preferir explorar o pipeline mais tarde e ver os resultados primeiro, vá para a próxima seção, **Acionar a execução do pipeline**.

![adf_overview](media/saas-tenancy-tenant-analytics/adf-data-factory.PNG)

Na página de visão geral, passe para a guia **Autor** no painel esquerdo e observe que três [pipelines](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) e três [conjuntos de dados](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services) foram criados.
![adf_author](media/saas-tenancy-tenant-analytics/adf_author_tab.JPG)

Os três pipelines aninhados são: SQLDBToDW, DBCopy e TableCopy.

O **Pipeline 1 – SQLDBToDW** pesquisa os nomes dos bancos de dados de locatário armazenados no banco de dados de Catálogo (nome da tabela: [__ShardManagement].[ShardsGlobal]) e, para cada banco de dados de locatário, executa o pipeline **DBCopy**. Após a conclusão, o esquema do procedimento armazenado **sp_TransformExtractedData** fornecido será executado. Esse procedimento armazenado transforma os dados carregados nas tabelas de preparo e preenche as tabelas de esquema em estrela.

O **Pipeline 2 – DBCopy** pesquisa os nomes das tabelas e colunas de origem de um arquivo de configuração armazenado no Armazenamento de Blobs.  Em seguida, o pipeline **TableCopy** é executado para cada uma das quatro tabelas: TicketFacts, CustomerFacts, EventFacts e VenueFacts. A atividade **[Foreach](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)** é executada em paralelo para os 20 bancos de dados. O ADF permite que um máximo de 20 iterações de loop sejam executadas em paralelo. Considere criar vários pipelines para mais bancos de dados.    

O **Pipeline 3 – TableCopy** usa números de versão de linha no Banco de Dados SQL (_rowversion_) para identificar linhas que foram alteradas ou atualizadas. Essa atividade pesquisa as versões de linha inicial e final para extrair linhas das tabelas de origem. A tabela **CopyTracker** armazenada em cada banco de dados de locatário rastreia a última linha extraída de cada tabela de origem em cada execução. Linhas novas ou alteradas são copiadas para as tabelas de preparo correspondentes no data warehouse: **raw_Tickets**, **raw_Customers**, **raw_Venues** e **raw_Events**. Por fim, a última versão de linha é salva na tabela **CopyTracker** para ser usada como a versão de linha inicial para a próxima extração. 

Há também três serviços vinculados parametrizados que vinculam o data factory aos Bancos de Dados SQL de origem, ao SQL Data Warehouse de destino e ao Armazenamento de Blobs intermediário. Na guia **Autor**, clique em **Conexões** para explorar os serviços vinculados, conforme mostrado na imagem a seguir:

![adf_linkedservices](media/saas-tenancy-tenant-analytics/linkedservices.JPG)

Há três conjuntos de dados correspondentes aos três serviços vinculados, que fazem referência aos dados usados nas atividades de pipeline como entradas ou saídas. Explore cada um dos conjuntos de dados para observar as conexões e os parâmetros usados. _AzureBlob_ aponta para o arquivo de configuração que contém as colunas e as tabelas de origem e de destino, bem como a coluna de rastreamento em cada fonte.
  
### <a name="data-warehouse-pattern-overview"></a>Visão geral do padrão do data warehouse
O SQL Data Warehouse é usado como repositório de análise para executar a agregação dos dados de locatário. Neste exemplo, o PolyBase é usado para carregar dados no SQL Data Warehouse. Dados brutos são carregados em tabelas de preparo que têm uma coluna de identidade para controlar as linhas que foram transformadas em tabelas da esquema em estrela. A imagem a seguir mostra o padrão de carga: ![loadingpattern](media/saas-tenancy-tenant-analytics/loadingpattern.JPG)

Tabelas de dimensão SCD (Dimensão de Alteração Lenta) tipo 1 são usadas neste exemplo. Cada dimensão tem uma chave alternativa definida usando uma coluna de identidade. Como melhor prática, a tabela de dimensão de data é preenchida previamente para economizar tempo. Para as outras tabelas de dimensão, uma instrução CTAS (CREATE TABLE AS SELECT)... é usada para criar uma tabela temporária que contém as linhas existentes modificadas e não modificadas, em conjunto com as chaves alternativas. Isso é feito com IDENTITY_INSERT = ON. Em seguida, novas linhas são inseridas na tabela com IDENTITY_INSERT = OFF. Para reverter facilmente, a tabela de dimensões existente é renomeada e a tabela temporária é renomeada para se tornar a nova tabela de dimensões. Antes de cada execução, a tabela de dimensões antiga é excluída.

Tabelas de dimensões são carregadas antes da tabela de fatos. Essa sequência garante que para cada fato que chegar, todas as dimensões referenciadas já existam. Conforme os fatos são carregados, é feita a correspondência da chave de negócio de cada dimensão respectiva e as chaves alternativas correspondentes são adicionadas a cada fato.

A etapa final da transformação exclui os dados de preparo prontos para a próxima execução do pipeline.
   
### <a name="trigger-the-pipeline-run"></a>Acionar a execução do pipeline
Siga as etapas abaixo para executar todo o pipeline de extração, carga e transformação para todos os bancos de dados de locatário:
1. Na guia **Autor** da interface do usuário do ADF, selecione o pipeline **SQLDBToDW** no painel esquerdo.
1. Clique em **Disparador** e o, do menu suspenso, clique em **Disparar Agora**. Essa ação executa o pipeline imediatamente. Em um cenário de produção, você definiria um cronograma para executar o pipeline para atualizar os dados de acordo com uma programação.
  ![adf_trigger](media/saas-tenancy-tenant-analytics/adf_trigger.JPG)
1. Na página **Execução de Pipeline**, clique em **Concluir**.
 
### <a name="monitor-the-pipeline-run"></a>Monitorar a execução de pipeline
1. Na interface do usuário do ADF, alterne para a guia **Monitor** no menu à esquerda.
1. Clique em **Atualizar** até que o status do pipeline SQLDBToDW seja **Com êxito**.
  ![adf_monitoring](media/saas-tenancy-tenant-analytics/adf_monitoring.JPG)
1. Conecte-se ao data warehouse usando o SSMS e consulte as tabelas de esquema em estrela para verificar se os dados foram carregados nessas tabelas.

Após a conclusão do pipeline, a tabela de fatos contém dados de vendas de ingressos de todas as casas de show e as tabelas de dimensões são preenchidas com as casas de show, os eventos e os clientes respectivos.

## <a name="data-exploration"></a>Exploração de dados

### <a name="visualize-tenant-data"></a>Visualizar dados de locatário

Os dados no esquema em estrela fornecem todos os dados de vendas de ingressos necessários para a análise. Visualizar os dados graficamente facilita a identificação de tendências em grandes conjuntos de dados. Nesta seção, você usa o **Power BI** para manipular e visualizar os dados de locatário no data warehouse.

Use as seguintes etapas para se conectar ao Power BI e importar os modos de exibição que você criou anteriormente:

1. Inicie o Power BI desktop.
2. Na faixa de opções Página Inicial, selecione **Obter Dados** e **Mais...** no menu.
3. Na janela **Obter Dados**, selecione **Banco de Dados SQL do Azure**.
4. Na janela de logon do banco de dados, digite o nome do servidor (**catalog-dpt-&lt;Usuário&gt;.database.windows.net**). Selecione **Importar** para **Modo de Conectividade de Dados** e, em seguida, clique em **OK**. 

    ![sign-in-to-power-bi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. Selecione **Banco de dados** no painel esquerdo, insira o nome de usuário = *developer*e digite a senha = *P@ssword1*. Clique em **Conectar**.  

    ![database-sign-in](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. No painel **Navegador**, no banco de dados de análise, selecione as tabelas de esquema em estrela: **fact_Tickets**, **dim_Events**, **dim_Venues**, **dim_Customers** e **dim_Dates**. Em seguida, selecione **Carregar**. 

Parabéns! Você carregou os dados no Power BI com êxito. Agora, explore visualizações interessantes para obter insights sobre seus locatários. Vamos examinar como a análise pode fornecer algumas recomendações controladas por dados para a equipe de negócios do Wingtip Tickets. As recomendações podem ajudar a otimizar a experiência de atendimento ao cliente e o modelo de negócios.

Comece analisando os dados de vendas de ingressos para ver a variação de uso entre as casas de shows. Selecione as opções exibidas no Power BI para plotar um gráfico de barras com o número total de ingressos vendidos por cada casa de shows. (Devido à variação aleatória no gerador de ingressos, seus resultados podem ser diferentes.)
 
![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues-DW.PNG)

A plotagem anterior confirma que o número de tíquetes vendidos por cada local varia. Locais que vendem mais tíquetes estão usando mais o serviço do que locais que vendem menos tíquetes. Pode haver uma oportunidade para ajustar a alocação de recursos de acordo com as necessidades de diferentes locatários.

Você poderá analisar melhor os dados para ver como as vendas de tíquetes variam ao longo do tempo. Selecione as opções mostradas na imagem a seguir no Power BI para plotar o número total de ingressos vendidos por dia em um período de 60 dias.
 
![SaleVersusDate](./media/saas-tenancy-tenant-analytics/SaleVersusDate-DW.PNG)

O gráfico anterior mostra que há picos de vendas de ingressos para algumas casas de show. Esses picos reforçam a ideia de que alguns locais talvez estejam consumindo recursos do sistema desproporcionalmente. Até o momento, não há um padrão óbvio para indicar quando ocorrem os picos.

Agora, vamos investigar a importância desses dias com picos de vendas. Quando esses picos ocorrem depois que os tíquetes começam a ser vendidos? Para plotar os ingressos vendidos por dia, selecione as opções mostradas na imagem a seguir no Power BI.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay-DW.PNG)

Esse gráfico mostra que algumas casas de show vendem grandes quantidades de ingressos no primeiro dia de venda. Assim que tíquetes estão à venda nesses locais, parece haver grande demanda. Esse aumento de atividade em alguns locais pode afetar o serviço para outros locatários.

Você pode analisar os dados novamente para ver se essa grande demanda ocorre para todos os eventos hospedados por esses locais. Em gráficos anteriores, você viu que o Contoso Concert Hall vende muitos ingressos e que o Contoso também apresenta picos nas vendas de ingressos em determinados dias. Experimente as opções do Power BI para plotar vendas cumulativas de tíquetes para a Contoso Concert Hall, concentrando-se nas tendências de vendas de cada um dos eventos. Todos os eventos seguem o mesmo padrão de venda? Tente produzir um gráfico como o mostrado abaixo.

![ContosoSales](media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

Este gráfico de vendas cumulativas de ingressos ao longo do tempo para o Contoso Concert Hall para cada evento mostra que o pico de vendas não ocorre para todos os eventos. Familiarize-se com as opções de filtro para explorar as tendências de vendas de outras casas de shows.

As percepções de padrões de vendas de tíquetes podem levar o Wingtip Tickets a otimizar seu modelo de negócios. Em vez de recarregar todos os locatários igualmente, talvez Wingtip deva introduzir as camadas de serviço com diferentes níveis de desempenho. Locais maiores que precisam vender mais tíquetes por dia podem receber a oferta de uma camada superior com um SLA (contrato de nível de serviço) superior. Esses locais podem ter seus bancos de dados colocados em pool com limites de recursos maiores por banco de dados. Cada camada de serviço pode ter uma alocação de vendas por hora, com valores adicionais cobrados por exceder a alocação. Locais maiores que têm picos de vendas periódicos pode se beneficiar dos níveis mais altos e Wingtip Tickets podem monetizar seus serviços com mais eficiência.

Enquanto isso, alguns clientes de Wingtip Tickets reclamam que se esforçam para vender tíquetes suficientes para justificar o custo do serviço. Talvez nessas percepções haja uma oportunidade de aumentar as vendas de tíquetes para locais com baixo desempenho. Vendas mais altas aumentariam o valor percebido do serviço. Clique com o botão direito do mouse em fact_Tickets e selecione **Nova medida**. Digite a seguinte expressão para a nova medida chamada **AverageTicketsSold**:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Selecione as opções de visualização a seguir para plotar os tíquetes de porcentagem vendidos em cada local para determinar o sucesso relativo.

![AvgTicketsByVenues](media/saas-tenancy-tenant-analytics/AvgTicketsByVenues-DW.PNG)

O gráfico acima mostra que, embora a maioria das casas de show venda mais de 80% de seus ingressos, algumas têm dificuldade para preencher mais da metade dos lugares. Familiarize-se com Values Well para selecionar a porcentagem máxima ou mínima de tíquetes vendidos para cada local.

## <a name="embedding-analytics-in-your-apps"></a>Inserindo a análise em seus aplicativos 
Este tutorial se concentrou na análise entre locatários usada para melhorar a compreensão que o fornecedor do software tem de seus locatários. A análise também pode fornecer insights para os _locatários_, ajudando-os a gerenciar seus negócios com mais eficácia. 

No exemplo do Wingtip Tickets, você descobriu que as vendas de ingressos tendem a seguir padrões previsíveis. Esse insight pode ser usado para ajudar as casas de show com baixo desempenho a promover as vendas de ingressos. Talvez haja uma oportunidade de empregar técnicas de aprendizado de máquina para prever as vendas de ingressos dos eventos. Os efeitos das alterações de preços também podem ser modelados, de forma a permitir que o impacto de ofertas de descontos seja previsto. O Power BI Embedded pode ser integrado a um aplicativo de gerenciamento de evento para visualizar as vendas previstas, incluindo o impacto dos descontos sobre o total de assentos vendidos e a receita em eventos com poucas vendas. Com o Power BI Embedded, você pode até mesmo integrar a aplicação do desconto aos preços dos ingressos diretamente na experiência de visualização.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Implantar um SQL Data Warehouse preenchido com um esquema em estrela para análise de locatário.
> * Usar o Azure Data Factory para extrair dados de cada banco de dados de locatário para o data warehouse de análise.
> * Otimize os dados extraídos (reorganize em um esquema de estrela).
> * Consultar o data warehouse de análise. 
> * Usar o Power BI para visualizar tendências em dados em todos os locatários.

Parabéns!

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais adicionais que aproveitam o aplicativo de SaaS do Wingtip](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
