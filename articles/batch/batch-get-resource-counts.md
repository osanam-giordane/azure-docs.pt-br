---
title: Contar os estados para tarefas e nós – Lote do Azure | Microsoft Docs
description: Conte o estado das tarefas do Lote do Azure e nós de computação para ajudar a gerenciar e monitorar soluções do Lote.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 06/29/2018
ms.author: danlep
ms.openlocfilehash: f4bad3d7058e82a246afce9502d275c7d485cb88
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011944"
---
# <a name="monitor-batch-solutions-by-counting-tasks-and-nodes-by-state"></a>Monitorar soluções do Lote pela contagem de tarefas e nós por estado

Para monitorar e gerenciar soluções do Lote do Azure em grande escala, você precisa de contagens precisas de recursos em vários estados. O Lote do Azure fornece operações eficientes para obter essas contagens para *tarefas* e *nós de computação* do Lote. Use essas operações em vez de chamadas à API potencialmente demoradas para retornar informações detalhadas sobre grandes coleções de nós ou de tarefas.

* [Obter Contagens de Tarefas][rest_get_task_counts] obtém uma contagem agregada de tarefas ativas, em execução e concluídas em um trabalho, bem como das tarefas que tiveram êxito ou falharam. 

  Contando as tarefas em cada estado, você pode exibir mais facilmente o andamento do trabalho para um usuário ou detectar atrasos inesperados ou falhas que podem afetar o trabalho. Obter Contagens de Tarefas está disponível desde a API do serviço de lote versão 2017-06-01.5.1 e SDKs e ferramentas relacionados.

* [Listar Contagens de Nó de Pool][rest_get_node_counts] obtém o número de nós de computação dedicado e de baixa prioridade em cada pool que estão em vários estados: criando, ocioso, offline, que admitiu preempção, reinicializando, refazendo a imagem, iniciando e outros. 

  Pela contagem de nós em cada estado, você pode determinar quando você tem recursos computacionais adequados para executar seus trabalhos e identificar possíveis problemas com seus pools. Listar Contagens de Nó de Pool está disponível desde a API do Serviço de Lote versão 2018-03-01.6.1 e SDKs e ferramentas relacionados.

Se você está usando uma versão do serviço que não dá suporte às operações de contagem de nós ou de contagem de tarefas, use em vez disso uma consulta de lista para contar esses recursos. Também use uma consulta de lista para obter informações sobre outros recursos de Lote, tais como aplicativos, arquivos e trabalhos. Para obter mais informações sobre a aplicação de filtros a consultas de lista, consulte [Criar consultas para listar recursos do Lote com eficiência](batch-efficient-list-queries.md).

## <a name="task-state-counts"></a>Contagens de estados de tarefa

A operação Obter Contagens de Tarefas conta tarefas pelos seguintes estados:

- **Ativa** – uma tarefa que está na fila e é capaz de executar, mas não está atualmente atribuída a um nó de computação. Uma tarefa também é `active` se é [dependente de uma tarefa pai](batch-task-dependencies.md) que ainda não foi concluída. 
- **Executando** – uma tarefa que foi atribuída a um nó de computação, mas ainda não foi concluída. Uma tarefa é contabilizada como `running` quando seu estado é `preparing` ou `running`, conforme indicado pela operação [Obter informações sobre uma tarefa][rest_get_task].
- **Concluída** – uma tarefa que não é mais elegível para ser executada porque foi concluída com êxito, ou então foi concluída com êxito e também esgotou seu limite de repetição. 
- **Bem-sucedida** – uma tarefa cujo resultado da execução da tarefa é `success`. O Lote determina se uma tarefa teve êxito ou não, verificando a propriedade `TaskExecutionResult` da propriedade [executionInfo][rest_get_exec_info].
- **Bem-sucedida** – uma tarefa cuja execução tem resultado `failure`.

O exemplo de código .NET a seguir mostra como recuperar contagens de tarefas por estado: 

```csharp
var taskCounts = await batchClient.JobOperations.GetTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
Console.WriteLine("ValidationStatus: {0}", taskCounts.ValidationStatus);
```

Você pode usar um padrão semelhante para REST e outras linguagens com suporte para obter as contagens de tarefa para um trabalho. 
 

### <a name="consistency-checking-for-task-counts"></a>Verificação de consistência para contagens de tarefas

O Lote fornece validação adicional para contagens de estado de tarefa, executando verificações de consistência em vários componentes do sistema. No evento improvável de a verificação de consistência encontrar erros, o Lote corrige o resultado da operação de obter contagens de tarefas com base nos resultados da verificação de consistência.

A propriedade `validationStatus` na resposta indica se o Lote executou ou não a verificação de consistência. Se o Lote não é capaz de verificar contagens de estado contra os estados reais mantidos no sistema, então a propriedade `validationStatus` é definida como `unvalidated`. Por motivos de desempenho, o Lote não executará a verificação de consistência se o trabalho incluir mais de 200.000 tarefas; portanto, a propriedade `validationStatus` poderá ser definida como `unvalidated` nesse caso. (A contagem de tarefas não é necessariamente errada nesse caso, pois até mesmo uma perda de dados limitada é improvável.) 

Quando uma tarefa muda de estado, o pipeline de agregação processa a alteração dentro de alguns segundos. A operação Obter Contagens de Tarefas reflete as contagens de tarefas atualizadas dentro desse período. No entanto, se o pipeline de agregação perder uma alteração em um estado de tarefa, essa alteração não será registrada até a próxima passagem de validação. Durante esse tempo, contagens de tarefa podem ser ligeiramente imprecisas devido ao evento perdido, mas elas serão corrigidas na próxima passagem de validação.

## <a name="node-state-counts"></a>Contagens de estados de nós

A operação Listar Contagens de Nós de Pool lista nós de computação pelos seguintes estados em cada pool. Contagens de agregação separadas são fornecidas para nós dedicados e nós de baixa prioridade em cada pool.

- **Criando** – uma VM alocada no Azure que ainda não iniciou o processo de ingresso em um pool.
- **Ociosa** – um nó de computação disponível que não está executando uma tarefa atualmente.
- **LeavingPool** – um nó que está saindo do pool, seja porque o usuário o removeu explicitamente ou porque o pool está redimensionando ou reduzindo verticalmente de modo automático.
- **Offline** – um nó que o Lote não pode usar para agendar novas tarefas.
- **Admitiu preempção** – um nó de baixa prioridade que foi removido do pool porque o Azure recuperou a VM. Um nó `preempted` pode ser reinicializado quando a capacidade da VM de baixa prioridade substituta está disponível.
- **Reinicializando** – um nó que está reinicializando.
- **Refazendo a imagem** – um nó no qual o sistema operacional está sendo reinstalado.
- **Em execução** – um nó que está executando uma ou mais tarefas (exceto a tarefa iniciar).
- **Iniciando** – um nó no qual o serviço de Lote está iniciando. 
- **StartTaskFailed** – um nó no qual a [tarefa iniciar][rest_start_task] falhou e esgotou todas as novas tentativas e no qual `waitForSuccess` está definido na tarefa iniciar. O nó não é utilizável para execução de tarefas.
- **Desconhecido** – um nó que perdeu contato com o serviço de Lote e cujo estado não é conhecido.
- **Não utilizável** – um nó que não pode ser usado para execução da tarefa devido a erros.
- **WaitingForStartTask** – um nó no qual a tarefa iniciar começou a ser executada, mas `waitForSuccess` está definida e a tarefa iniciar não foi concluída.

O seguinte trecho de C# mostra como listar as contagens de nó para todos os pools na conta atual:

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts())
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```
O seguinte trecho de C# mostra como listar as contagens de nó para um determinado pool na conta atual.

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts(new ODATADetailLevel(filterClause: "poolId eq 'testpool'")))
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```
Você pode usar um padrão semelhante para REST e outras linguagens compatíveis para obter contagens de nó para pools.
 
## <a name="next-steps"></a>Próximas etapas

* Veja a [visão geral dos recursos do Lote](batch-api-basics.md) para saber mais sobre os conceitos e os recursos do serviço do Lote. O artigo aborda os recursos principais do Lote, como pools, nós de computação, trabalhos e tarefas, e fornece uma visão geral dos recursos do serviço.

* Para obter informações sobre a aplicação de filtros a consultas que listam recursos do Lote, veja [Criar consultas para listar recursos do Lote com eficiência](batch-efficient-list-queries.md).


[rest_get_task_counts]: /rest/api/batchservice/job/gettaskcounts
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
[rest_get_task]: /rest/api/batchservice/task/get
[rest_list_tasks]: /rest/api/batchservice/task/list
[rest_get_exec_info]: /rest/api/batchservice/task/get#executionInfo
[rest_start_task]: /rest/api/batchservice/pool/add#starttask

