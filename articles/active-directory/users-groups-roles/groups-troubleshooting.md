---
title: Solução de problemas de associação dinâmica para grupos| Microsoft Docs
description: Solução de problemas para a associação dinâmica para grupos no Azure AD.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: aaded644635ab93cef9323ad38d1d150b15fe743
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448712"
---
# <a name="troubleshooting-dynamic-memberships-for-groups"></a>Solucionando problemas de associações dinâmicas a grupos
**Configurei uma regra em um grupo, mas nenhuma associação foi atualizada no grupo**<br/>Verifique os valores dos atributos de usuário na regra: há usuários que atendem à regra? Se tudo estiver correto, aguarde alguns instantes para que o grupo seja populado. Dependendo do tamanho do seu locatário, o grupo pode levar até 24 horas para ser populado pela primeira vez ou depois de uma alteração de regra.

**Configurei uma regra, mas agora os membros da regra existentes foram removidos**<br/>Este comportamento é esperado. Membros existentes do grupo são removidos quando uma regra é habilitada ou alterada. Os usuários retornados da avaliação da regra são adicionados como membros ao grupo.     

**Não vejo as alterações da associação instantaneamente quando adiciono ou altero uma regra, por que não?**<br/>A avaliação de associação dedicada é feita periodicamente em um processo assíncrono em segundo plano. O tempo que o processo leva é determinado pelo número de usuários no diretório e pelo tamanho do grupo criado como resultado da regra. Normalmente, os diretórios com um pequeno número de usuários verão as alterações de associação de grupo em poucos minutos. Os diretórios com um grande número de usuários podem levar até 30 minutos ou mais para serem populados.

### <a name="next-steps"></a>Próximas etapas
Esses artigos fornecem mais informações sobre o Active Directory do Azure.

* [Gerenciamento de acesso a recursos com grupos do Active Directory do Azure](../fundamentals/active-directory-manage-groups.md)
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](../active-directory-apps-index.md)
* [O que é o Active Directory do Azure?](../fundamentals/active-directory-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](../connect/active-directory-aadconnect.md)
