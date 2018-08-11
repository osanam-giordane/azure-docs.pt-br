---
title: Visão Geral dos Conjuntos de dimensionamento de máquinas virtuais do Azure | Microsoft Docs
description: Saiba mais sobre conjunto de dimensionamento de máquinas virtuais e como dimensionar automaticamente os aplicativos
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 03/27/2018
ms.author: negat
ms.openlocfilehash: 8ded9b20bd70d18b8a68df0c9775f3a56f8b185b
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2018
ms.locfileid: "30247368"
---
# <a name="what-are-virtual-machine-scale-sets"></a>O que são conjuntos de escala de máquina virtual (Virtual machine Scale Set)?
Os conjuntos de dimensionamento de máquinas virtuais do Azure lhe permitem criar e gerenciar um grupo de VMs idênticas e com balanceamento de carga. O número de instâncias de VM pode aumentar ou diminuir automaticamente em resposta à demanda ou a um agendamento definido. Os conjuntos de dimensionamento fornecem alta disponibilidade para seus aplicativos e permitem que você gerencie, configure e atualize um grande número de máquinas virtuais de forma centralizada. Com conjuntos de dimensionamento de máquinas virtuais, você pode criar serviços em grande escala para áreas como computação, big data e cargas de trabalho de contêiner.


## <a name="why-use-virtual-machine-scale-sets"></a>Para que usar Conjuntos de Dimensionamento de Máquinas Virtuais?
Para fornecer redundância e melhorar o desempenho, os aplicativos geralmente são distribuídos entre várias instâncias. Os clientes podem acessar seu aplicativo por meio de um balanceador de carga que distribui solicitações para uma das instâncias do aplicativo. Se você precisar fazer manutenção ou atualizar uma instância do aplicativo, os clientes deverão ser distribuídos para outra instância do aplicativo disponível. Para atender à demandas de cliente adicionais, talvez seja necessário aumentar o número de instâncias de aplicativos que executam seu aplicativo.

Os conjuntos de dimensionamento de máquinas virtuais fornecem os recursos de gerenciamento para aplicativos que são executados em várias VMs, [dimensionamento automático de recursos](virtual-machine-scale-sets-autoscale-overview.md) e balanceamento de carga do tráfego. Os conjuntos de dimensionamento oferecem as principais vantagens abaixo:

- **Facilidade de criar e gerenciar várias VMs**
    - Quando você tem muitas VMs que executam seu aplicativo, é importante manter uma configuração consistente em seu ambiente. Para um desempenho confiável do seu aplicativo, o tamanho da VM, a configuração do disco e a instalação do aplicativo devem corresponder em todas as VMs.
    - Com conjuntos de dimensionamento, todas as instâncias de VM são criadas da mesma imagem e da mesma configuração do sistema operacional base. Essa abordagem permite gerenciar facilmente centenas de VMs sem outras tarefas de configuração ou gerenciamento de rede.
    - Os conjuntos de dimensionamento dão suporte ao uso do [balanceador de carga do Azure](../load-balancer/load-balancer-overview.md) para distribuição de tráfego de camada 4 básico e ao [Gateway de Aplicativo do Azure](../application-gateway/application-gateway-introduction.md) para distribuição do tráfego de camada 7 e terminação SSL mais avançadas.

- **Fornece alta disponibilidade e resiliência do aplicativo**
    - Os conjuntos de dimensionamento são usados para executar várias instâncias do aplicativo. Se uma dessas instâncias de VM tem um problema, os clientes continuam a acessar o aplicativo por meio de uma das outras instâncias de VM com o mínimo de interrupção.
    - Para obter mais disponibilidade, você pode usar [Zonas de disponibilidade](../availability-zones/az-overview.md) para distribuir automaticamente instâncias de VM em um conjunto de dimensionamento em um único datacenter ou em vários datacenters.

- **Permite que seu aplicativo dimensione automaticamente de acordo com as alterações de demanda de recursos**
    - A demanda do cliente pelo seu aplicativo pode mudar ao longo do dia ou da semana. De acordo com a demanda do cliente, os conjuntos de dimensionamento podem aumentar o número de instâncias de VM automaticamente, de acordo com o aumento da demanda de aplicativo, e reduzir o número de instâncias de VM de acordo com a redução de demanda.
    - O dimensionamento automático também minimiza o número de instâncias de VM desnecessárias que executam seu aplicativo quando a demanda está baixa, enquanto os clientes continuam a receber um nível de desempenho aceitável conforme a demanda cresce e as instâncias de VM adicionais são adicionadas automaticamente. Esse recurso ajuda a reduzir os custos e a criar recursos do Azure de forma eficiente conforme a necessidade.

- **Funciona em larga escala**
    - Os conjuntos de dimensionamento dão suporte a até mil instâncias de VM. Se você criar e carregar suas próprias imagens VM personalizadas, o limite será de 300 instâncias de VM.
    - Para obter o melhor desempenho com cargas de trabalho de produção, use o [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md) e o [Armazenamento Premium](../virtual-machines/windows/premium-storage.md).


## <a name="differences-between-virtual-machines-and-scale-sets"></a>Diferenças entre máquinas virtuais e conjuntos de dimensionamento
Os conjuntos de dimensionamento são criados a partir de máquinas virtuais. Com conjuntos de dimensionamento, as camadas de automação e gerenciamento são fornecidas para executar e dimensionar seus aplicativos. Você pode criar e gerenciar VMs individuais manualmente, ou integrar ferramentas existentes para criar um nível de automação semelhante. A tabela a seguir descreve os benefícios dos conjuntos de dimensionamento em comparação com o gerenciamento manual de várias instâncias de VM.

| Cenário                           | Grupo manual de VMs                                                                    | Conjunto de escala de máquina virtual |
|------------------------------------|----------------------------------------------------------------------------------------|---------------------------|
| Adicionar outras instâncias de VM        | Processo manual de criar, configurar e garantir a conformidade                             | Criar automaticamente usando a configuração central |
| Balanceamento e distribuição de tráfego | Processo manual de criar e configurar o balanceador de carga do Azure ou o Gateway de Aplicativo      | Pode criar e integrar com o balanceador de carga do Azure ou o Gateway de Aplicativo automaticamente |
| Alta disponibilidade e redundância   | Criar Conjunto de Disponibilidade manualmente ou distribuir e controlar VMs entre Zonas de Disponibilidade | Distribuição automática de instâncias de VM em Zonas de Disponibilidade ou Conjuntos de Disponibilidade |
| Dimensionamento de VMs                     | Monitoramento manual e a Automação do Azure                                                 | Dimensionamento automático com base em métricas de host, métricas no convidado, Application Insights ou agendamento |

Não há nenhum custo adicional para usar os conjuntos de dimensionamento. Você paga apenas pelos recursos de computação subjacentes, como as instâncias de VM, o balanceador de carga ou o armazenamento do disco gerenciado. Os recursos de automação e gerenciamento, como o dimensionamento automático e a redundância, não incorrem em nenhum custo adicional pelo uso das VMs.


## <a name="next-steps"></a>Próximas etapas
Para começar, crie seu primeiro conjunto de dimensionamento de máquinas virtuais no Portal do Azure.

> [!div class="nextstepaction"]
> [Criar um conjunto de dimensionamento usando o Portal do Azure](quick-create-portal.md)
