---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 07/18/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: a9aa4680d91a4ab95933ae636c4467217fd162b2
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2018
ms.locfileid: "39278046"
---
| Recurso | Destino | Limite rígido |
|----------|--------------|------------|
| Serviços de sincronização de armazenamento por assinatura | 15 serviços de sincronização de armazenamento | Não  |
| Grupos de sincronização por Serviço de Sincronização de Armazenamento | 30 grupos de sincronização | SIM |
| Servidores registrados por Serviço de Sincronização de Armazenamento | 99 servidores | SIM |
| Pontos de extremidade de nuvem por grupo de sincronização | 1 ponto de extremidade de nuvem | SIM |
| Pontos de extremidade de servidor por grupo de sincronização | 50 pontos de extremidade de servidor | Não  |
| Pontos de extremidade de servidor por servidor | 33-99 pontos de extremidade de servidor | Sim, mas varia com base na configuração |
| Tamanho do ponto de extremidade | 4 TiB | Não  |
| Objetos do sistema de arquivos (diretórios e arquivos) por grupo de sincronização | 25 milhões de objetos | Não  |
| Número máximo de objetos do sistema de arquivos (diretórios e arquivos) em um diretório | 200.000 objetos | SIM |
| Tamanho máximo do nome do objeto (diretórios e arquivos) | 255 caracteres | SIM |
| Tamanho do arquivo | 100 GiB | Não  |
| Tamanho mínimo do arquivo para que um arquivo seja colocado em camadas | 64 KiB | SIM |