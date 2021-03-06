---
title: Depurar consultas do Azure Stream Analytics usando SELECT INTO
description: Este artigo descreve como exemplo de consulta intermediária de dados no trabalho do Azure Stream Analytics usando instruções SELECT INTO na sintaxe de consulta.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: ccaa6203e4bfe52758e26416646f9152ac5378ea
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
ms.locfileid: "30907948"
---
# <a name="debug-queries-by-using-select-into-statements"></a>Depurar consultas usando instruções SELECT INTO

No processamento de dados em tempo real, saber qual será a aparência dos dados no meio da consulta pode ser útil. Como as entradas ou etapas de um trabalho do Stream Analytics do Azure podem ser lidas várias vezes, você pode escrever instruções SELECT INTO extras. Ao fazer isso, dados intermediários são gerados no armazenamento que permitem a você inspecionar a exatidão dos dados, assim como as *variáveis de inspeção* fazem quando você depura um programa.

## <a name="use-select-into-to-check-the-data-stream"></a>Usar SELECT INTO para verificar o fluxo de dados

O exemplo de consulta a seguir em um trabalho do Stream Analytics do Azure tem uma entrada de fluxo, duas entradas de dados de referência e uma saída para o Armazenamento de Tabelas do Azure. A consulta une dados do hub de eventos e dois blobs de referência para obter informações de nome e categoria:

![Exemplo de consulta SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Observe que o trabalho está em execução, mas não há eventos sendo gerados na saída. No bloco **Monitoramento**, mostrado aqui, você pode ver que a entrada está gerando dados, mas não dá para saber qual etapa de **JOIN** fez com que todos os eventos fossem removidos.

![O bloco Monitoramento](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)
 
Nesse caso, é possível adicionar algumas instruções extras SELECT INTO para "registrar em log" os resultados intermediários de JOIN e os dados que são lidos da entrada.

Neste exemplo, adicionamos duas novas "saídas temporárias". Elas podem ser qualquer coletor desejado. Aqui, usamos o Armazenamento do Azure como um exemplo:

![Adicionando instruções SELECT INTO extras](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

Assim, você pode reescrever a consulta desta forma:

![Consulta SELECT INTO reescrita](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Agora, reinicie o trabalho e deixe ele ser executado por alguns minutos. Em seguida, consulte temp1 e temp2 com o Cloud Explorer do Visual Studio para gerar as seguintes tabelas:

**temp1 table**
![SELECT INTO temp1 table](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**temp2 table**
![SELECT INTO temp2 table](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Como você pode ver, temp1 e temp2 têm dados e a coluna de nome é preenchida corretamente em temp2. No entanto, como ainda não há dados na saída, algo está errado:

![SELECT INTO output1 table with no data](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Pela amostragem de dados, você pode ter quase certeza de que o problema é com o segundo JOIN. Você pode baixar os dados de referência do blob e dar uma olhada:

![SELECT INTO ref table](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Como você pode ver, o formato do GUID nos dados de referência é diferente do formato da coluna [from] em temp2. Por esse motivo, os dados não chegaram em output1 como esperado.

É possível corrigir o formato dos dados, carregá-los no blob de referência e tentar novamente:

![SELECT INTO temp table](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Dessa vez, os dados na saída são formatados e preenchidos conforme esperado.

![SELECT INTO final table](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)


## <a name="get-help"></a>Obter ajuda

Para obter mais assistência, experimente nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

