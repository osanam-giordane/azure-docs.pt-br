---
title: Como especificar variáveis de ambiente para serviços no Azure Service Fabric | Microsoft Docs
description: Mostra como usar variáveis de ambiente para aplicativos no Service Fabric
documentationcenter: .net
author: mikkelhegn
manager: markfuss
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: 4325b3acd3cbc73ee5976021bebe96c267b2a6dd
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206021"
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>Como especificar variáveis de ambiente para serviços no Service Fabric

Este artigo mostra como especificar variáveis de ambiente para um serviço ou contêiner no Service Fabric.

## <a name="procedure-for-specifying-environment-variables-for-services"></a>Procedimento para especificar variáveis de ambiente para serviços

Neste exemplo, você pode definir uma variável de ambiente para um contêiner. O artigo supõe que você já tem um manifesto de aplicativo e serviço.

1. Abra o arquivo ServiceManifest.xml.
1. No elemento `CodePackage`, adicione um novo elemento `EnvironmentVariables` e um elemento `EnvironmentVariable` para cada variável de ambiente.

    ```xml
      <CodePackage Name="MyCode" Version="CodeVersion1">
      ...
        <EnvironmentVariables>
          <EnvironmentVariable Name="MyEnvVariable" Value="DeafultValue"/>
          <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentVariables>
      </CodePackage>
    ```

    As variáveis de ambiente podem ser substituídas no manifesto do aplicativo.

1. Para substituir essas variáveis de ambiente no manifesto do aplicativo, use o elemento `EnvironmentOverrides`.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre alguns dos principais conceitos que são discutidos neste artigo, veja os artigos sobre [Gerenciar aplicativos de vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md).

Para obter informações sobre outras funcionalidades de gerenciamento de aplicativo disponíveis no Visual Studio, confira [Gerenciar seus aplicativos do Service Fabric no Visual Studio](service-fabric-manage-application-in-visual-studio.md).