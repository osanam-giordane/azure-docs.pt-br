---
title: Configurar alertas de integridade do serviço do Azure com o PagerDuty | Microsoft Docs
description: Obtenha notificações personalizadas sobre eventos de integridade do serviço na instância do PagerDuty.
author: shawntabrizi
manager: scotthit
editor: ''
services: service-health
documentationcenter: service-health
ms.assetid: ''
ms.service: service-health
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: shtabriz
ms.openlocfilehash: 5f6f3f61b5f7a06ac4056499edfb811780838cdc
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39441870"
---
# <a name="configure-service-health-alerts-with-pagerduty"></a>Configurar alertas de integridade do serviço com o PagerDuty

Este artigo mostra como configurar notificações de integridade do serviço do Azure por meio do PagerDuty usando um webhook. Usando o tipo de integração personalizada do Microsoft Azure [PagerDuty](https://www.pagerduty.com/), você pode adicionar alertas de Integridade do Serviço com facilidade a serviços do PagerDuty novos ou existentes.

## <a name="creating-a-service-health-integration-url-in-pagerduty"></a>Criando uma URL de integração de integridade do serviço no PagerDuty
1.  Verifique se você se inscreveu e entrou em sua conta do [PagerDuty](https://www.pagerduty.com/).

1.  Navegue para a seção **Serviços** do PagerDuty.

    ![A seção “Serviços” do PagerDuty](./media/webhook-alerts/pagerduty-services-section.png)

1.  Selecione **Adicionar Novo Serviço** ou abra um serviço existente configurado.

1.  Nas **Configurações de Integração**, selecione o seguinte:

    a. **Tipo de Integração**: Microsoft Azure

    b. **Nome da Integração**: \<Nome\>

    ![As “Configurações de Integração” do PagerDuty](./media/webhook-alerts/pagerduty-integration-settings.png)

1.  Preencha todos os outros campos obrigatórios e selecione **Adicionar**.

1.  Abra essa nova integração e copie e salve a **URL de Integração**.

    ![A “URL de Integração” do PagerDuty](./media/webhook-alerts/pagerduty-integration-url.png)

## <a name="create-an-alert-using-pagerduty-in-the-azure-portal"></a>Criar um alerta usando o PagerDuty no portal do Azure
### <a name="for-a-new-action-group"></a>Para um novo grupo de ação:
1. Siga as etapas 1 a 8 de [Criar um alerta em uma notificação de integridade do serviço para um novo grupo de ação usando o portal do Azure](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md).

1. Defina na lista de **Ações**:

    a. **Tipo de Ação:** *Webhook*

    b. **Detalhes:** a **URL de Integração** do PagerDuty salva anteriormente.

    c. **Nome:** o nome, alias ou identificador do webhook.

1. Selecione **Salvar** quando concluir a criação do alerta.

### <a name="for-an-existing-action-group"></a>Para um grupo de ação existente:
1. No [portal do Azure](https://portal.azure.com/), selecione **Monitor**.

1. Na seção **Configurações**, selecione **Grupos de ação**.

1. Encontre e selecione o grupo de ação que você deseja editar.

1. Adicione à lista de **Ações**:

    a. **Tipo de Ação:** *Webhook*

    b. **Detalhes:** a **URL de Integração** do PagerDuty salva anteriormente.

    c. **Nome:** o nome, alias ou identificador do webhook.

1. Selecione **Salvar** quando concluir a atualização do grupo de ação.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testando a integração de webhook por meio de uma solicitação HTTP POST
1. Crie o conteúdo de integridade do serviço que você deseja enviar. Encontre um conteúdo de webhook de integridade do serviço de exemplo em [Webhooks para alertas do log de atividades do Azure](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

1. Crie uma solicitação HTTP POST, da seguinte maneira:

    ```
    POST        https://events.pagerduty.com/integration/<IntegrationKey>/enqueue

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Você deverá receber um `202 Accepted` com uma mensagem que contém a “ID do evento”.

1. Acesse o [PagerDuty](https://www.pagerduty.com/) para confirmar se a integração foi configurada com êxito.

## <a name="next-steps"></a>Próximas etapas
- Saiba como [configurar notificações de webhook para sistemas de gerenciamento de problemas existentes](service-health-alert-webhook-guide.md).
- Examine o [esquema do webhook de alertas de log de atividades](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- Saiba mais sobre as [notificações de integridade do serviço](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- Saiba mais sobre [grupos de ação](../monitoring-and-diagnostics/monitoring-action-groups.md).