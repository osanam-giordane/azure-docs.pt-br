---
title: Decodificação de edifact B2B dos Aplicativos Lógicos resolve UNH2.5 – Aplicativos Lógicos do Azure | Microsoft Docs
description: Decodificação de edifact B2B dos Aplicativos Lógicos resolve UNH2.5
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 3e04e7515a8ddfe512ba2efbbd45c3d1b571c1fd
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35297940"
---
# <a name="how-to-handle-edifact-documents-having-unh25-segment"></a>Como tratar documentos EDIFACT tendo o segmento UNH2.5
Quando está presente no documento EDIFACT, UNH2.5 está sendo usado para pesquisa de esquema. 

Exemplo: o campo UNH é **EAN008** na mensagem EDIFACT  
UNH+SSDD1+ORDERS:D:03B:UN:**EAN008**'  

Etapas a seguir para manipular a mensagem 
1. Atualizar o esquema
2. Verificar as configurações do contrato  

## <a name="update-the-schema"></a>Atualizar o esquema
Para processar a mensagem, você precisa implantar um esquema com o nome do nó raiz UNH2.5.  Para dar um exemplo, o nome raiz do esquema seria **EFACT_D03B_ORDERS_EAN008**  

Para cada D03B_ORDERS com um segmento UNH2.5 diferente, você precisaria implantar um esquema individual.  

## <a name="add-schema-to-the-edifact-agreement"></a>Adicionar o esquema ao contrato de EDIFACT
### <a name="edifact-decode"></a>Decodificação de EDIFACT
Para decodificar a mensagem de entrada, configure o esquema nas definições de recebimento do contrato de EDIFACT
1. Adicionar o esquema à conta de integração    
2. Configure o esquema nas definições de recebimento do contrato de EDIFACT. 
3. Selecione o contrato de EDIFACT e clique em **Editar como JSON**.  Adicionar o valor UNH2.5 no contrato de recebimento **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>Codificação de EDIFACT
Para codificar a mensagem de entrada, configure o esquema nas definições de envio do contrato de EDIFACT
1. Adicionar o esquema à conta de integração    
2. Configure o esquema nas definições de envio do contrato de EDIFACT. 
3. Selecione o contrato de EDIFACT e clique em **Editar como JSON**.  Adicione o valor UNH2.5 no contrato de envio **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre os contratos da conta de integração](../logic-apps/logic-apps-enterprise-integration-agreements.md "Saiba mais sobre os contratos de integração corporativa")  