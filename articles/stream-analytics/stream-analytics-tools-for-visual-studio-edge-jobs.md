---
title: Trabalhos do Edge nas ferramentas do Azure Stream Analytics para Visual Studio
description: Este artigo descreve como criar, depurar e criar trabalhos de Borda do Stream Analytics usando as ferramentas do Stream Analytics para Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/13/2018
ms.openlocfilehash: cfdfa1493857a1731da74c52a616293b482f811a
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030685"
---
# <a name="develop-stream-analytics-edge-jobs-using-visual-studio-tools"></a>Desenvolver trabalhos de Borda do Stream Analytics utilizando ferramentas do Visual Studio

Neste tutorial, você aprenderá a utilizar as ferramentas do Stream Analytics para o Visual Studio criar, depurar e criar trabalhos de Borda do Stream Analytics. Após criar e testar o trabalho, você poderá acessar o Portal do Azure para implantá-lo nos dispositivos. 

## <a name="prerequisites"></a>pré-requisitos

Você precisará dos seguintes pré-requisitos para concluir este tutorial:

* Instale o [Visual Studio 2017](https://www.visualstudio.com/downloads/), [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/) ou [Visual Studio 2013 Update 4](https://www.microsoft.com/download/details.aspx?id=45326). As edições Enterprise (Ultimate/Premium), Professional, Community têm suporte. Não há suporte para a Edição Express.  

* Siga as [instruções de instalação](stream-analytics-tools-for-visual-studio-edge-jobs.md) para instalar as ferramentas do Stream Analytics para o Visual Studio.
 
## <a name="create-a-stream-analytics-edge-project"></a>Criar um projeto de Borda do Azure Stream Analytics 

No Visual Studio, selecione **Aquivo** > **Novo** > **Projeto**. Navegue até a lista de **Modelos** à esquerda > expanda **Azure Stream Analytics** > **Borda do Stream Analytics** > **Aplicativo de Borda do Azure Stream Analytics**. Forneça um Nome, Local e nome da Solução para o projeto e selecione **OK**.

![Novo projeto de Borda](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-edge-project.png)

Depois que o projeto for criado, navegue para o **Gerenciador de Soluções** para exibir a hierarquia de pastas.

![Exibição do gerenciador de soluções](./media/stream-analytics-tools-for-visual-studio-edge-jobs/edge-project-in-solution-explorer.png)

 
## <a name="choose-the-correct-subscription"></a>Escolha a assinatura correta

1. No menu **Exibir** do Visual Studio, selecione **Gerenciador de Servidores**.  

2. Clique com o botão direito do mouse em **Azure** > Selecione **Conectar-se à assinatura do Microsoft Azure** > e, em seguida, faça logon com a conta do Azure.

## <a name="define-inputs"></a>Definir entradas

1. No **Gerenciador de Soluções**, expanda o nó de **Entradas** e você deverá ver uma entrada nomeada **EdgeInput.json**. Clique duas vezes para exibir as configurações.  

2. Certifique-se de que o Tipo de Origem esteja definido como **Fluxo de Dados** > Origem definida como **Hub de Borda** > Formato de Serialização de Evento definido como **Json** > e a Codificação definida como **UTF8**. Opcionalmente, é possível renomear o **Alias de Entrada**, mas para este exemplo manteremos como está. Se for renomear o alias de entrada, utilize o nome especificado durante a definição da consulta. Clique em **Salvar** para salvar as configurações.  
   ![Configuração de entrada](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-input-configuration.png)
 


## <a name="define-outputs"></a>Definir saídas

1. No **Gerenciador de Soluções**, expanda o nó de **Saídas** e você deverá ver uma saída nomeada **EdgeOutput.json**. Clique duas vezes para exibir as configurações.  

2. Certifique-se de que o Coletor esteja definido para selecionar **Hub de Borda** > Formato de Serialização de Evento definido como **Json** > Codificação definida como **UTF8** > e Formato definido como **Matriz**. Opcionalmente, é possível renomear o **Alias de Saída**, mas para este exemplo manteremos como está. Se for renomear o alias de saída, utilize o nome especificado durante a definição da consulta. Clique em **Salvar** para salvar as configurações. 
   ![Configuração de saída](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>Definir a consulta de transformação

Os trabalhos do Stream Analytics implantados nos ambientes de Borda têm suporte para a maior parte da [Referência da Linguagem de Consulta do Stream Analytics](https://msdn.microsoft.com/azure/stream-analytics/reference/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396), no entanto, as operações a seguir ainda não têm suporte para trabalhos de Borda: 


|**Categoria**  | **Comando**  |
|---------|---------|
|Operadores geoespaciais |<ul><li>CreatePoint</li><li>CreatePolygon</li><li>CreateLineString</li><li>ST_DISTANCE</li><li>ST_WITHIN</li><li>ST_OVERLAPS</li><li>ST_INTERSECTS</li></ul> |
|Outros operadores | <ul><li>PARTIÇÃO POR</li><li>CARIMBO DE DATA/HORA POR MAIS DE</li><li>DISTINTO</li><li>Parâmetro de expressão no operador COUNT</li><li>Microssegundo nas funções de DATA e HORA</li><li>JavaScript UDA (esse recurso ainda está em versão prévia para trabalhos implantados na nuvem)</li></ul>   |

Ao criar um trabalho de Borda no portal, o compilador avisará automaticamente se você não estiver utilizando um operador com suporte.

No Visual Studio, defina a consulta de transformação a seguir no editor de consultas (**script.asaql file**)

```sql
SELECT * INTO EdgeOutput
FROM EdgeInput 
```

## <a name="test-the-job-locally"></a>Testar o trabalho localmente

Para testar a consulta localmente, será necessário carregar os dados de exemplo. É possível obter dados de exemplo, baixando dados do Registro do [Repositório GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/Registration.json) e salve-o no computador local. 

1. Para fazer carregar os dados de exemplo, > clique com o botão direito do mouse no arquivo **EdgeInput.json** > escolha **Adicionar Entrada Local**  

2. Na janela pop-up > **Procure** os dados de exemplo do caminho local > Selecione **Salvar**.
   ![Configuração de entrada local](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-local-input-configuration.png)
 
3. Um arquivo nomeado **local_EdgeInput.json** é adicionado automaticamente à pasta de entradas.  
4. é possível executá-lo localmente ou enviar para o Azure. Para testar a consulta > Selecione **Executar localmente**.  
   ![Opções de execução](./media/stream-analytics-tools-for-visual-studio-edge-jobs/run-options.png)
 
5. A janela do prompt de comando mostra o status do trabalho. Quando o trabalho executa com êxito, ele cria uma pasta semelhante a “2018-02-23-11-31-42” no caminho da pasta do projeto “Visual Studio 2015\Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42”. Navegue até o caminho da pasta para exibir os resultados na pasta local:

   Também é possível entrar no Portal do Azure e verificar se o trabalho foi criado. 

   ![Pasta de resultados](./media/stream-analytics-tools-for-visual-studio-edge-jobs/result-folder.png)

## <a name="submit-the-job-to-azure"></a>Enviar o trabalho para o Azure

1. Antes de enviar o trabalho para o Azure, será necessário conectar-se à Assinatura do Azure. Abra **Gerenciador de Servidores** > clique com o botão direito em **Azure** > **Conectar-se à assinatura do Microsoft Azure** > entre na sua assinatura do Azure.  

2. Para enviar o trabalho para o Azure, navegue até o editor de consultas > selecione **Enviar para o Azure**.  

3. Uma janela pop-up é aberta, onde é possível escolher atualizar um trabalho de Borda existente ou criar um novo. Ao atualizar um trabalho existente, ele substituirá toda a configuração do trabalho; nesse cenário, será publicado um novo trabalho. Selecione **Criar um novo trabalho do Azure Stream Analytics** > insira um nome para o trabalho, semelhante a **MyASAEdgeJob** > escolha a **Assinatura** exigida, **Grupo de Recursos** e o **Local** > Selecione **Enviar**.

   ![Enviar para o Azure](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-to-azure.png)
 
   Agora que o trabalho de Borda do Azure Stream Analytics foi criado, é possível consultar o [tutorial Executar trabalhos no IoT Edge](stream-analytics-edge.md) para saber como implantá-lo nos dispositivos. 

## <a name="manage-the-job"></a>Gerenciar o trabalho 

É possível exibir o status do trabalho e o diagrama do trabalho no Gerenciador de Servidores. No **Gerenciador de Servidores** > **Stream Analytics** > expanda a assinatura e o grupo de recursos onde você implantou o trabalho de Borda > é possível visualizar MyASAEdgejob com o status **Criado**. Expanda o nó de trabalho e clique duas vezes nele para abrir a exibição de trabalho.

![Opções do gerenciador de servidores](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
A janela de exibição de trabalhos fornece operações como atualizar o trabalho, excluir o trabalho, abrir o trabalho no Portal do Azure e etc.

![Diagrama do trabalho e outras opções](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>Próximas etapas

* [Mais informações sobre o Azure Iot Edge](../iot-edge/about-iot-edge.md)
* [ASA no tutorial de IoT Edge](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Enviar comentários para a equipe usando esta pesquisa](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
