---
title: Implantar o serviço de aplicativo em um ambiente offline na pilha do Azure | Microsoft Docs
description: Orientações detalhadas sobre como implantar o serviço de aplicativo em um ambiente desconectado do Azure pilha protegido pelo AD FS.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2018
ms.author: anwestg
ms.openlocfilehash: 7084243c0fc84429b585c3e8fd9e5c64df469ec4
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604277"
---
# <a name="add-an-app-service-resource-provider-to-a-disconnected-azure-stack-environment-secured-by-ad-fs"></a>Adicionar um provedor de recursos do serviço de aplicativo para um ambiente desconectado do Azure pilha protegido pelo AD FS

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

> [!IMPORTANT]
> Aplicar a atualização 1804 a seu sistema de pilha do Azure integradas ou implantar o kit de desenvolvimento de pilha do Azure mais recente antes de implantar 1.2 de serviço de aplicativo do Azure.
>
>

Seguindo as instruções neste artigo, você pode instalar o [provedor de recursos do serviço de aplicativo](azure-stack-app-service-overview.md) para um ambiente de pilha do Azure:

- não conectado à Internet
- protegido pelos serviços de Federação do Active Directory (AD FS).

Para adicionar o provedor de recursos do serviço de aplicativo para sua implantação do Azure pilha offline, você deve concluir essas tarefas de nível superior:

1. Concluir o [etapas de pré-requisito](azure-stack-app-service-before-you-get-started.md) (como comprar certificados, que pode levar de alguns dias para receber).
2. [Baixe e extraia os arquivos de instalação e auxiliar](azure-stack-app-service-before-you-get-started.md) para um computador conectado à Internet.
3. Crie um pacote de instalação offline.
4. Execute o arquivo do instalador appservice.exe.

## <a name="create-an-offline-installation-package"></a>Criar um pacote de instalação offline

Para implantar o serviço de aplicativo em um ambiente desconectado, primeiro você deve criar um pacote de instalação offline em um computador que está conectado à Internet.

1. Execute o instalador de AppService.exe em um computador que está conectado à Internet.

2. Clique em **avançado** > **criar pacote de instalação offline**.

    ![Instalador de serviço de aplicativo][1]

3. O instalador do serviço de aplicativo cria um pacote de instalação offline e exibe o caminho para ele. Você pode clicar em **Abrir pasta** para abrir a pasta em que o Explorador de arquivos.

    ![Instalador de serviço de aplicativo](media/azure-stack-app-service-deploy-offline/image02.png)

4. Copie o instalador (AppService.exe) e o pacote de instalação offline para sua máquina de host de pilha do Azure.

## <a name="complete-the-offline-installation-of-app-service-on-azure-stack"></a>Concluir a instalação offline do serviço de aplicativo na pilha do Azure

1. Execute appservice.exe como um administrador de um computador que pode alcançar o ponto de extremidade de gerenciamento de recursos do Azure pilha administração do Azure.

2. Clique em **avançado** > **concluir a instalação offline**.

    ![Instalador de serviço de aplicativo][2]

3. Navegue até o local do pacote de instalação offline que você criou anteriormente e, em seguida, clique em **próximo**.

    ![Instalador de serviço de aplicativo](media/azure-stack-app-service-deploy-offline/image04.png)

4. Revise e aceite os termos de licença de Software da Microsoft e, em seguida, clique em **próximo**.

5. Revise e aceite os termos de licença de terceiros e, em seguida, clique em **próximo**.

6. Certifique-se de que as informações de configuração de nuvem do serviço de aplicativo estão corretas. Se você usou as configurações padrão durante a implantação do Kit de desenvolvimento de pilha do Azure, você pode aceitar os valores padrão aqui. No entanto, se você personalizou as opções quando implantou o Azure pilha ou estiver implantando em um sistema integrado, você deve editar os valores nesta janela de refletir isso. Por exemplo, se você usar o mycloud.com de sufixo de domínio, altere seu ponto de extremidade do Gerenciador de recursos do Azure pilha locatário do Azure para gerenciamento. <region>. mycloud.com. Depois de confirmar suas informações, clique em **próximo**.

    ![Instalador de serviço de aplicativo][3]

7. Na próxima página:
    1. Clique no **conectar** lado a **assinaturas de pilha do Azure** caixa.
        - Fornece sua conta de administrador. Por exemplo, cloudadmin@azurestack.local. Digite sua senha e clique em **entrar**.
    2. No **assinaturas do Azure pilha** caixa, selecione a **assinatura do provedor padrão**.
    
    > [!NOTE]
    > Serviço de aplicativo só pode ser implantado no **assinatura do provedor padrão** neste momento.  Em uma atualização futura do serviço de aplicativo será implantado para a nova assinatura medição introduzido no Azure pilha 1804 e todas as implantações existentes serão migradas para essa nova assinatura também.
    >
    >
    
    3. No **Azure pilha locais** , selecione o local que corresponde à região que você está implantando. Por exemplo, selecione **local** se sua implantação para o Kit de desenvolvimento de pilha do Azure.
    4. Clique em **Próximo**.

    ![Instalador de serviço de aplicativo][4]

8. Agora você tem a opção de implantar em uma rede Virtual existente, conforme configurado pelas etapas [aqui](azure-stack-app-service-before-you-get-started.md#virtual-network), ou permitir que o instalador do serviço de aplicativo criar uma rede Virtual e sub-redes associadas.
    1. Selecione **criar redes com as configurações padrão**, aceite os padrões e, em seguida, clique em **próximo**, ou;
    2. Selecione **usar rede virtual existente e sub-redes**.
        1. Selecione o **grupo de recursos** que contém sua rede Virtual;
        2. Escolher o correto **rede Virtual** nome que você deseja implantar em;
        3. Selecione o correto **sub-rede** valores para cada uma das sub-redes função necessários;
        4. Clique em **Avançar**

    ![Instalador de serviço de aplicativo][5]

9. Insira as informações para o compartilhamento de arquivo e, em seguida, clique em **próximo**. O endereço do compartilhamento de arquivos deve usar o nome de domínio totalmente qualificado ou o endereço IP do servidor de arquivos. Por exemplo, \\\appservicefileserver.local.cloudapp.azurestack.external\websites, ou \\\10.0.0.1\websites

    > [!NOTE]
    > O instalador tentará testar a conectividade com o compartilhamento de arquivos antes de continuar.  No entanto, se você optar por implantar em uma rede Virtual existente, o instalador não pode ser capaz de se conectar ao compartilhamento de arquivos e exibe um aviso perguntando se deseja continuar.  Verifique as informações de compartilhamento de arquivos e continuar se elas estão corretas.
    >
    >

   ![Instalador de serviço de aplicativo][8]

10. Na próxima página:
    1. No **ID do aplicativo de identidade** , digite o GUID para o aplicativo que você está usando para identidade (do AD do Azure).
    2. No **arquivo de certificado de identidade de aplicativo** caixa, digite (ou procure) o local do arquivo do certificado.
    3. No **senha do certificado de identidade de aplicativo** caixa, digite a senha do certificado. Essa senha é aquele que você anotou quando você usou o script para criar os certificados.
    4. No **arquivo do certificado raiz do Azure Resource Manager** caixa, digite (ou procure) o local do arquivo do certificado.
    5. Clique em **Próximo**.

    ![Instalador de serviço de aplicativo][10]

11. Para cada um dos três caixas de arquivo de certificado, clique em **procurar** e, em seguida, navegue até o arquivo de certificado apropriado. Você deve fornecer a senha para cada certificado. Esses certificados são aqueles que você criou no [etapa de certificados necessários crie](azure-stack-app-service-before-you-get-started.md#get-certificates). Clique em **próximo** depois de inserir todas as informações.

    | Box | Exemplo de nome de arquivo de certificado |
    | --- | --- |
    | **Arquivo de certificado SSL do serviço de aplicativo padrão** | \_.appservice.local.AzureStack.external.pfx |
    | **Arquivo de certificado SSL de API do serviço de aplicativo** | api.appservice.local.AzureStack.external.pfx |
    | **Arquivo de certificado de SSL de publicador do serviço de aplicativo** | ftp.appservice.local.AzureStack.external.pfx |

    Se você usou um sufixo de domínio diferente quando você criou os certificados, os nomes de arquivo de certificado não usam *local. AzureStack.external*. Em vez disso, use as informações de domínio personalizado.

    ![Instalador de serviço de aplicativo][11]

12. Insira os detalhes do SQL Server para a instância do servidor usada para hospedar os bancos de dados do provedor de recursos do serviço de aplicativo e, em seguida, clique em **próximo**. O instalador valida as propriedades de conexão SQL. Você **deve** digite o endereço ip interno ou o nome de domínio totalmente qualificado para o nome do SQL Server.

    > [!NOTE]
    > O instalador tentará testar a conectividade com o SQl Server antes de continuar.  No entanto, se você optar por implantar em uma rede Virtual existente, o instalador não pode ser capaz de se conectar ao SQL Server e exibe um aviso perguntando se deseja continuar.  Verifique as informações do SQL Server e continuar se elas estão corretas.
    >
    >
   
   ![Instalador de serviço de aplicativo][12]

13. Examine as opções de SKU e a instância de função. Os padrões são preenchidos com o número mínimo de instância e o SKU mínimo para cada função em uma implantação de ASDK. Um resumo dos requisitos de memória e vCPU é fornecido para ajudar a planejar a implantação. Depois de fazer suas seleções, clique em **próximo**.

     > [!NOTE]
     > Para implantações de produção, siga as orientações em [planejamento de capacidade de funções de servidor do serviço de aplicativo do Azure na pilha do Azure](azure-stack-app-service-capacity-planning.md).
     >
     >

    | Função | Instâncias mínimas | SKU mínimo | Observações |
    | --- | --- | --- | --- |
    | Controller | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Gerencia e mantém a integridade da nuvem do serviço de aplicativo. |
    | Gerenciamento | 1 | Standard_A2 - (vCPUs 2, 3584 MB) | Gerencia os pontos de extremidade do serviço de aplicativo do Azure Resource Manager e a API, extensões portais (administrador, locatário, portal de funções) e o serviço de dados. Para dar suporte a failover, aumentado instâncias recomendadas para 2. |
    | Publicador | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Publica o conteúdo por meio de implantação da web e FTP. |
    | FrontEnd | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Encaminha solicitações para aplicativos de serviço de aplicativo. |
    | Trabalho compartilhados | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Hosts ou aplicativos de API aplicativos web e funções do Azure. Você talvez queira adicionar mais instâncias. Como um operador, você pode definir sua oferta e escolha qualquer camada SKU. As camadas devem ter no mínimo um vCPU. |

    ![Instalador de serviço de aplicativo][14]

    > [!NOTE]
    > **Windows Server 2016 Core não é uma imagem de plataforma com suporte para uso com o serviço de aplicativo do Azure na pilha do Azure.  Não use imagens de avaliação para implantações de produção.**

14. No **selecione a imagem de plataforma** caixa, escolha sua imagem de máquina virtual de implantação do Windows Server 2016 disponíveis no provedor de recursos de computação para a nuvem de serviço de aplicativo. Clique em **Próximo**.

15. Na próxima página:
     1. Insira o nome de usuário do administrador de máquina virtual de função de trabalho e a senha.
     2. Insira o nome de usuário do administrador de máquina virtual de outras funções e a senha.
     3. Clique em **Próximo**.

    ![Instalador de serviço de aplicativo][16]

16. Na página de resumo:
    1. Verifique se as seleções feitas por você. Para fazer alterações, use o **anterior** botões para visitar a páginas anteriores.
    2. Se as configurações estão corretas, selecione a caixa de seleção.
    3. Para iniciar a implantação, clique em **próximo**.

    ![Instalador de serviço de aplicativo][17]

17. Na próxima página:
    1. Acompanhar o progresso da instalação. Serviço de aplicativo no Azure pilha leva cerca de 60 minutos para implantar com base nas seleções padrão.
    2. Depois que a instalação for concluída com êxito, clique em **saída**.

    ![Instalador de serviço de aplicativo][18]

## <a name="validate-the-app-service-on-azure-stack-installation"></a>Validar o serviço de aplicativo na instalação de pilha do Azure

1. No portal de administração de pilha do Azure, vá para **administração - serviço de aplicativo**.

2. Na visão geral em status, verifique que o **Status** mostra **todas as funções estão prontas**.

    ![Gerenciamento de serviço de aplicativo](media/azure-stack-app-service-deploy/image12.png)
    
> [!NOTE]
> Se você optar por implantar em uma rede virtual existente e um endereço IP interno para conenct para seu servidor de arquivos, você deve adicionar uma regra de segurança de saída, permitindo que o tráfego entre a sub-rede de trabalho e o servidor de arquivos SMB.  Para fazer isso, vá para o WorkersNsg no Portal de administração e adicionar uma regra de segurança de saída com as seguintes propriedades:
> * Origem: qualquer
> * Intervalo de porta de origem: *
> * Destino: Endereços IP
> * Intervalo de endereço IP de destino: intervalo de endereços IP para o servidor de arquivos
> * Intervalo de porta de destino: 445
> * Protocolo: TCP
> * Ação: permitir
> * Prioridade: 700
> * Nome: Outbound_Allow_SMB445
>

## <a name="test-drive-app-service-on-azure-stack"></a>Faça o test drive do serviço de aplicativo na pilha do Azure

Depois de implantar e registrar o provedor de recursos do serviço de aplicativo, testá-lo para certificar-se de que os usuários podem implantar aplicativos web e API.

> [!NOTE]
> Você precisa criar uma oferta que tenha o namespace Microsoft Web dentro do plano. Em seguida, você precisa ter uma assinatura de locatário se inscreve para esta oferta. Para obter mais informações, consulte [criar oferta](azure-stack-create-offer.md) e [criar plano](azure-stack-create-plan.md).
>
Você *deve* tem uma assinatura de locatário para criar aplicativos que usam o serviço de aplicativo na pilha do Azure. Os recursos de apenas um administrador de serviço pode ser concluída dentro do portal de administração relacionados para a administração de provedor de recursos de serviço de aplicativo. Esses recursos incluem a capacidade de adicionar, configurando fontes de implantação e adicionar camadas de trabalhador e SKUs.
>
A partir do terceiro technical preview, funções de aplicativos criar a web, API e o Azure, você deve usar o portal de locatário e ter uma assinatura de locatário.

1. No portal de locatário de pilha do Azure, clique em **novo** > **Web + móvel** > **aplicativo Web**.

2. Sobre o **aplicativo Web** folha, digite um nome no **aplicativo Web** caixa.

3. Em **grupo de recursos**, clique em **novo**. Digite um nome no **grupo de recursos** caixa.

4. Clique em **Plano do Serviço de Aplicativo/Local** > **Criar Novo**.

5. No **plano de serviço de aplicativo** folha, digite um nome no **plano de serviço de aplicativo** caixa.

6. Clique em **preço** > **compartilhado gratuito** ou **compartilhado compartilhado** > **selecione**  >   **Okey** > **criar**.

7. Em menos de um minuto, um bloco para o novo aplicativo web é exibida no painel. Clique no bloco.

8. Sobre o **aplicativo Web** folha, clique em **procurar** para exibir o site padrão para este aplicativo.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Implantar um site de WordPress, DNN ou Django (opcional)

1. No portal de locatário de pilha do Azure, clique em **+**, vá para o Azure Marketplace, implantar um site Django e aguardar a conclusão bem-sucedida. O web platform Django usa um banco de dados do arquivo baseado no sistema. Ele não requer que os provedores de recursos adicionais, como SQL ou MySQL.

2. Se você também implantou um provedor de recursos do MySQL, você pode implantar um site de WordPress do Marketplace. Quando você for solicitado para parâmetros de banco de dados, digite o nome de usuário como *User1@Server1*com o nome de usuário e o nome do servidor de sua escolha.

3. Se você também implantou um provedor de recursos do SQL Server, você pode implantar um site DNN do Marketplace. Quando você for solicitado para parâmetros de banco de dados, escolha um banco de dados no computador executando o SQL Server que está conectado ao seu provedor de recursos.

## <a name="next-steps"></a>Próximas etapas

Você também pode experimentar outros [plataforma como um serviço (PaaS) serviços](azure-stack-tools-paas-services.md).

- [Provedor de recursos do SQL Server](azure-stack-sql-resource-provider-deploy.md)
- [Provedor de recursos MySQL](azure-stack-mysql-resource-provider-deploy.md)

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525

<!--Image references-->
[1]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-create-package.png
[2]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-complete-offline.png
[3]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-arm-endpoints.png
[4]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-subscription-information.png
[5]: ./media/azure-stack-app-service-deploy-offline/app-service-default-VNET-config.png
[6]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config.png
[7]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config-with-values.png
[8]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration.png
[9]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration-error.png
[10]: ./media/azure-stack-app-service-deploy-offline/app-service-identity-app.png
[11]: ./media/azure-stack-app-service-deploy-offline/app-service-certificates.png
[12]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration.png
[13]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration-error.png
[14]: ./media/azure-stack-app-service-deploy-offline/app-service-cloud-quantities.png
[15]: ./media/azure-stack-app-service-deploy-offline/app-service-windows-image-selection.png
[16]: ./media/azure-stack-app-service-deploy-offline/app-service-role-credentials.png
[17]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-deployment-summary.png
[18]: ./media/azure-stack-app-service-deploy-offline/app-service-deployment-progress.png
