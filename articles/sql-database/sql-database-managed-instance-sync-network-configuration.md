---
title: Serviço de Aplicativo do Azure - configuração de rede de sincronização | Microsoft Docs
description: Este artigo descreve como sincronizar sua configuração de rede a o plano de hospedagem do Serviço de Aplicativo do Azure.
ms.service: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.custom: managed instance
ms.topic: conceptual
ms.date: 03/07/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 57c4dd523a5dffc48a2d2d403d2a440a8d6cde95
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257881"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>Sincronizar a configuração de rede ao plano de hospedagem do Serviço de Aplicativo do Azure

Pode acontecer que, embora você tenha [integrado seu aplicativo à uma Rede Virtual do Azure](../app-service/web-sites-integrate-with-vnet.md), você não consiga estabelecer conexão à Instância Gerenciada. Algo que você pode é tentar atualizar a configuração de rede para o plano de serviço. 

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>Sincronizar a configuração de rede ao plano de hospedagem de Serviço de Aplicativo do Azure

Para fazer isso, siga estas etapas:  

1. Vá até o seu plano do Serviço de Aplicativo dos aplicativos web.
 
   ![plano do serviço de aplicativo](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. Clique em **Rede** e, em seguida, clique em **Clique aqui para gerenciar**.
 
   ![gerenciar plano de serviço](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. Selecione seu **VNet** e clique em **Sincronizar rede**. 
 
   ![sincronizar rede](./media/sql-database-managed-instance-sync-networking/sync.png)

4. Aguarde até que a sincronização seja feita.
  
   ![sincronização concluída](./media/sql-database-managed-instance-sync-networking/sync-done.png)

Agora você está pronto para tentar restabelecer a conexão à Instância Gerenciada.

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre como configurar sua rede virtual à Instância Gerenciada, consulte [Configuração da rede virtual de instância gerenciada](sql-database-managed-instance-vnet-configuration.md).
