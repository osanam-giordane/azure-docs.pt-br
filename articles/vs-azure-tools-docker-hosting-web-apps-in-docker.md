---
title: Implantar um contêiner do Docker do ASP.NET no ACR (Registro de Contêiner do Azure) | Microsoft Docs
description: Saiba como usar as Ferramentas do Visual Studio para Docker para implantar um aplicativo Web do ASP.NET Core em um registro de contêiner
services: azure-container-service
documentationcenter: .net
author: mlearned
manager: douge
editor: ''
ms.assetid: e5e81c5e-dd18-4d5a-a24d-a932036e78b9
ms.service: azure-container-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/21/2018
ms.author: mlearned
ms.openlocfilehash: 58df17b17de1d93683875b68dd7c6c087bc6d16d
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972301"
---
# <a name="deploy-an-aspnet-container-to-a-container-registry-using-visual-studio"></a>Implantar um contêiner do ASP.NET em um registro de contêiner usando o Visual Studio
## <a name="overview"></a>Visão geral
O Docker é um mecanismo de contêiner leve, semelhante em alguns pontos a uma máquina virtual, que você pode usar para hospedar aplicativos e serviços.
Este tutorial orienta sobre o uso do Visual Studio para publicar o aplicativo em contêiner em um [Registro de Contêiner do Azure](https://azure.microsoft.com/services/container-registry).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/dotnet/?utm_source=acr-publish-doc&utm_medium=docs&utm_campaign=docs) antes de começar.

## <a name="prerequisites"></a>pré-requisitos
Para concluir este tutorial:

* Instale a última versão do [Visual Studio 2017](https://azure.microsoft.com/downloads/) com a carga de trabalho "ASP.NET e desenvolvimento para a Web"
* Instalar o [Docker para Windows](https://docs.docker.com/docker-for-windows/install/)

## <a name="1-create-an-aspnet-core-web-app"></a>1. Criar um aplicativo Web ASP.NET Core
As etapas a seguir guiam você na criação de um aplicativo básico ASP.NET Core que será usado neste tutorial.

[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-publish-your-container-to-azure-container-registry"></a>2. Publicar o contêiner no Registro de Contêiner do Azure
1. Clique com o botão direito no projeto em **Gerenciador de Soluções** e escolha **Publicar**.
2. Na caixa de diálogo de destino de publicação, selecione a guia **Registro de Contêiner**.
3. Escolha **Novo Registro de Contêiner do Azure** e clique em **Publicar**.
4. Preencha os valores desejados em **Criar um novo Registro de Contêiner do Azure**.

    | Configuração      | Valor sugerido  | DESCRIÇÃO                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Prefixo DNS** | Nome globalmente exclusivo | Nome que identifica exclusivamente o registro de contêiner. |
    | **Assinatura** | Escolha sua assinatura | A assinatura do Azure a utilizar. |
    | **[Grupo de Recursos](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Nome do grupo de recursos no qual criar o registro de contêiner. Escolha **Novo** para criar um novo grupo de recursos.|
    | **[SKU](https://docs.microsoft.com/azure/container-registry/container-registry-skus)** | Standard | Camada de serviço do registro de contêiner  |
    | **Local do Registro** | Um local próximo | Escolha um Local em uma [região](https://azure.microsoft.com/regions/) próxima a você ou perto de outros serviços que usarão o registro de contêiner. |
    ![Caixa de diálogo Criar um Registro de Contêiner do Azure do Visual Studio][0]
5. Clique em **Criar**

Agora, é possível extrair o contêiner do registro para qualquer host capaz de executar imagens do Docker, por exemplo [Instâncias de Contêiner do Azure](./container-instances/container-instances-tutorial-deploy-app.md).

[0]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/vs-acr-provisioning-dialog.png
