---
title: Guia de publicação da oferta de aplicativo gerenciado de Aplicativos do Azure
description: Este artigo descreve os requisitos para publicar um aplicativo gerenciado no Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: 674e91a7c1de026a26cf9a3bf1eaead7f5e86144
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059188"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Aplicativos do Azure: Guia de publicação da oferta de aplicativo gerenciado

Os Modelos de Solução são uma das principais formas de publicar uma solução no Marketplace. Use este guia para reconhecer os requisitos dessa oferta. 

Estas são ofertas de transação que são implantadas e cobradas pelo Marketplace. A chamada à ação que um usuário vê é "Obtenha agora."

Use o aplicativo do Azure: tipo de oferta de aplicativo gerenciado quando as seguintes condições forem necessárias:
- Você pode implantar uma solução baseada em assinatura para o cliente usando uma VM ou uma solução inteira com base em IaaS.
- Você ou seu cliente exigem que a solução seja gerenciada por um parceiro.

>[!NOTE]
>Por exemplo, um parceiro pode ser um SI ou MSP (provedor de serviços gerenciados).  

## <a name="managed-application-offer"></a>Oferta de aplicativo gerenciado

|Requisitos |Detalhes  |
|---------|---------|
|Implantado na assinatura do Azure de um cliente | Os aplicativos gerenciados devem ser implantados na assinatura do cliente e podem ser gerenciados por terceiros | 
|Cobrança e medição    |  Os recursos serão provisionados na assinatura do Azure do cliente. As máquinas virtuais pago conforme o uso (PAYGO) serão negociadas com o cliente via Microsoft, faturadas por meio da assinatura do Azure do cliente (PAYGO) 
No caso de trazer sua própria licença, enquanto a Microsoft irá cobrar os custos de infraestrutura incorridos na assinatura do cliente, você irá transacionar os valores de licenciamento de software diretamente para o cliente        |
|VHD (disco rígido virtual) compatível com Azure    |   As VMs devem ser criadas em Windows ou Linux.<ul> <li>Para obter mais informações sobre como criar um VHD do Linux, visite a seção Criar um VHD compatível com o Azure (baseado em Linux) localizada em [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based).</li> <li>Para obter mais informações sobre como criar um VHD do Windows, visite a seção Criar um VHD compatível com o Azure (baseado em Windows) localizada em [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based).</li> </ul>      |

>[!NOTE]
> Aplicativos gerenciados devem poder ser implantados por meio do Marketplace. Se a comunicação do cliente é relevante, você deve falar com clientes interessados depois de habilitar o compartilhamento de leads.  


## <a name="next-steps"></a>Próximas etapas
Caso ainda não tenha feito isso, 

- [Registrar](https://azuremarketplace.microsoft.com/sell) no marketplace

Se você estiver registrado e criando uma nova oferta ou trabalhando em uma existente,

- [Faça logon no Portal do Cloud Partner](https://cloudpartner.azure.com) para criar ou concluir a oferta
