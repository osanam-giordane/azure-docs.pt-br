---
title: Como configurar identidades atribuídas pelo sistema e pelo usuário em uma VM do Azure usando REST
description: Instruções passo a passo para configurar identidades atribuídas pelo usuário e pelo sistema em uma VM do Azure usando o CURL para fazer chamadas à API REST.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/25/2018
ms.author: daveba
ms.openlocfilehash: 7926944f329665af2df287d120bd9f4a8ee78380
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39433925"
---
# <a name="configure-managed-identity-on-an-azure-vm-using-rest-api-calls"></a>Configurar identidade gerenciada em uma VM do Azure usando chamadas à API REST

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

A Identidade Gerenciada fornece aos serviços do Azure uma identidade de sistema gerenciada automaticamente no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, você aprenderá como realizar as seguintes operações de identidade gerenciada em uma VM do Azure, usando o CURL para fazer chamadas ao ponto de extremidade REST do Azure Resource Manager:

- Habilitar e desabilitar a identidade atribuída pelo sistema em uma VM do Azure
- Adicionar e remover uma identidade atribuída pelo sistema em uma VM do Azure

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com a Identidade de Serviço Gerenciada, consulte a [seção de visão geral](overview.md). **Verifique se examinou a [diferença entre uma identidade atribuída pelo sistema e uma atribuída pelo usuário](overview.md#how-does-it-work)**.
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para realizar as operações de gerenciamento deste artigo, a conta precisará das seguintes atribuições de função:
    - [Colaborador da Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) para criar uma VM e habilitar e remover a identidade gerenciada atribuída ao usuário e/ou sistema de uma VM do Azure.
    - Função de [Colaborador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) para criar uma identidade atribuída ao usuário.
    - Função de [Operador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator) para atribuir e remover uma identidade atribuída ao usuário de e para uma VM.
- Se você estiver usando o Windows, instale o [subsistema do Windows para Linux](https://msdn.microsoft.com/commandline/wsl/about) ou use o [Azure Cloud Shell](../../cloud-shell/overview.md) no portal do Azure.
- Se você usa o [subsistema do Windows para Linux](https://msdn.microsoft.com/commandline/wsl/about) ou um [SO de distribuição do Linux](/cli/azure/install-azure-cli-apt?view=azure-cli-latest), [instale o console local da CLI do Azure](/azure/install-azure-cli).
- Se você estiver usando o console local da CLI do Azure, entre no Azure usando `az login` com uma conta associada à assinatura do Azure que você deseja para gerenciar as identidades atribuídas pelo sistema ou pelo usuário.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>Identidade atribuída pelo sistema

Nesta seção, você aprenderá como habilitar e desabilitar a identidade atribuída pelo sistema em uma VM do Azure usando o CURL para fazer chamadas ao ponto de extremidade REST do Azure Resource Manager.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>Habilitar identidade atribuída ao sistema durante a criação de uma VM do Azure

Para criar uma VM do Azure com uma identidade atribuída pelo sistema habilitada, você precisa criar uma VM e recuperar um token de acesso para usar o CURL para chamar o ponto de extremidade do Resource Manager com o valor do tipo de identidade atribuído pelo sistema.

1. Criar um [grupo de recursos](../../azure-resource-manager/resource-group-overview.md#terminology) para contenção e implantação de VM e seus recursos relacionados usando [az group create](/cli/azure/group/#az-group-create). Ignore esta etapa, se você já tiver o grupo de recursos que deseja usar:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Crie um [adaptador de rede](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) para a VM:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Recupere um token de acesso de portador, que será usado na próxima etapa no cabeçalho de autorização para criar a VM com uma identidade gerenciada atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Crie uma VM usando o CURL para chamar o ponto de extremidade REST do Azure Resource Manager. O exemplo a seguir cria uma VM chamada *myVM* com uma identidade atribuída pelo sistema, identificada no corpo da solicitação pelo valor `"identity":{"type":"SystemAssigned"}`. Substitua `<ACCESS TOKEN>` pelo valor recebido na etapa anterior quando você solicitou um token de acesso de portador e o valor de `<SUBSCRIPTION ID>` apropriado para seu ambiente.
 
    ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"TestVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
    ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Habilitar a identidade atribuída pelo sistema em uma VM existente do Azure

Para habilitar a identidade atribuída pelo sistema em uma VM existente, você precisa adquirir um token de acesso e, em seguida, usar o CURL para chamar o ponto de extremidade REST do Resource Manager para atualizar o tipo de identidade.

1. Recupere um token de acesso de portador, que será usado na próxima etapa no cabeçalho de autorização para criar a VM com uma identidade gerenciada atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Use o seguinte comando CURL para chamar o ponto de extremidade REST do Azure Resource Manager para habilitar a identidade atribuída pelo sistema na VM, identificada no corpo da solicitação pelo valor `{"identity":{"type":"SystemAssigned"}`, para uma VM chamada *myVM*.  Substitua `<ACCESS TOKEN>` pelo valor recebido na etapa anterior quando você solicitou um token de acesso de portador e o valor de `<SUBSCRIPTION ID>` apropriado para seu ambiente.
   
   > [!IMPORTANT]
   > Para garantir que você não exclua nenhuma identidade gerenciada atribuída pelo usuário existente que esteja atribuída à VM, será necessário listar as identidades atribuídas pelo usuário usando este comando CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Se houver alguma identidade atribuída pelo usuário à VM identificada no valor `identity` na resposta, passe para a etapa 3 que mostra como reter identidades atribuídas pelo usuário ao habilitar a identidade atribuída pelo sistema na VM.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Para habilitar a identidade atribuída pelo sistema em uma VM com identidades atribuídas pelo usuário existentes, você precisará adicionar `SystemAssigned` ao valor `type`.  
   
   Por exemplo, se a VM tiver as identidades atribuídas pelo usuário `ID1` e `ID2` atribuídas a ela, e você desejar adicionar uma identidade atribuída pelo sistema à VM, use a seguinte chamada do CURL. Substitua `<ACCESS TOKEN>` e `<SUBSCRIPTION ID>` pelos valores apropriados para seu ambiente.
   
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/TestVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Desabilitar uma identidade atribuída pelo sistema de uma VM do Azure

Para desabilitar uma identidade atribuída pelo sistema em uma VM existente, você precisa adquirir um token de acesso e, em seguida, usar o CURL para chamar o ponto de extremidade REST do Resource Manager para atualizar o tipo de identidade para `None`.

1. Recupere um token de acesso de portador, que será usado na próxima etapa no cabeçalho de autorização para criar a VM com uma identidade gerenciada atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Atualize a VM usando o CURL para chamar o ponto de extremidade REST do Azure Resource Manager para desabilitar a identidade atribuída pelo sistema.  O exemplo a seguir desabilita uma identidade atribuída pelo sistema, identificada no corpo da solicitação pelo valor `{"identity":{"type":"None"}}`, de uma VM chamada *myVM*.  Substitua `<ACCESS TOKEN>` pelo valor recebido na etapa anterior quando você solicitou um token de acesso de portador e o valor de `<SUBSCRIPTION ID>` apropriado para seu ambiente.

   > [!IMPORTANT]
   > Para garantir que você não exclua nenhuma identidade gerenciada atribuída pelo usuário existente que esteja atribuída à VM, será necessário listar as identidades atribuídas pelo usuário usando este comando CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Se houver alguma identidade atribuída pelo usuário à VM identificada no valor `identity` na resposta, passe para a etapa 3 que mostra como reter identidades atribuídas pelo usuário ao desabilitar a identidade atribuída pelo sistema na VM.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Para remover a identidade atribuída pelo sistema de uma VM que tenha identidades atribuídas pelo usuário, remova `SystemAssigned` do valor `{"identity":{"type:" "}}` e mantenha o valor `UserAssigned` e a matriz `identityIds`, que definem quais identidades atribuídas pelo usuário são atribuídas à VM.

## <a name="user-assigned-identity"></a>Identidade atribuída pelo usuário

Nesta seção, você aprenderá como adicionar e remover uma identidade atribuída pelo usuário em uma VM do Azure usando o CURL para fazer chamadas ao ponto de extremidade REST do Azure Resource Manager.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-an-azure-vm"></a>Atribuir uma identidade atribuída pelo usuário durante a criação de uma VM do Azure

1. Recupere um token de acesso de portador, que será usado na próxima etapa no cabeçalho de autorização para criar a VM com uma identidade gerenciada atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Crie um [adaptador de rede](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) para a VM:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Recupere um token de acesso de portador, que será usado na próxima etapa no cabeçalho de autorização para criar a VM com uma identidade gerenciada atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Crie uma identidade atribuída pelo usuário usando as instruções encontradas aqui: [Criar uma identidade gerenciada atribuída pelo usuário](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Crie uma VM usando o CURL para chamar o ponto de extremidade REST do Azure Resource Manager. O exemplo a seguir cria uma VM chamada *myVM* no grupo de recursos *myResourceGroup* com uma identidade atribuída pelo usuário `ID1`, identificada no corpo da solicitação pelo valor `"identity":{"type":"UserAssigned"}`. Substitua `<ACCESS TOKEN>` pelo valor recebido na etapa anterior quando você solicitou um token de acesso de portador e o valor de `<SUBSCRIPTION ID>` apropriado para seu ambiente.
 
   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM",{"identity":{"type":"UserAssigned", "identityIds":["/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"TestVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-vm"></a>Atribuir uma identidade atribuída pelo usuário a uma VM do Azure existente

1. Recupere um token de acesso de portador, que será usado na próxima etapa no cabeçalho de autorização para criar a VM com uma identidade gerenciada atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Crie uma identidade atribuída pelo usuário usando as instruções encontradas aqui: [Criar uma identidade gerenciada atribuída pelo usuário](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3.  Para garantir que você não exclua nenhuma identidade gerenciada atribuída pelo usuário ou pelo sistema existente que esteja atribuída à VM, será necessário listar os tipos de identidades atribuídos à VM usando o comando CURL a seguir. Se houver identidades gerenciadas atribuídas ao conjunto de dimensionamento de máquinas virtuais, elas serão listadas no valor `identity`.

    ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    Se houver alguma identidade atribuída pelo usuário ou pelo sistema à VM identificada no valor `identity` na resposta, passe para a etapa 5 que mostra como reter identidades atribuídas pelo usuário ao habilitar a identidade atribuída pelo sistema na VM.

4. Se não houver nenhuma identidade atribuída pelo usuário à VM, use o seguinte comando CURL para chamar o ponto de extremidade REST do Azure Resource Manager para atribuir a primeira identidade atribuída pelo usuário à VM.  Se houver alguma identidade atribuída pelo usuário à VM, passe para a próxima etapa que mostra como adicionar várias identidades atribuídas pelo usuário a uma VM.

   O exemplo a seguir atribui uma identidade atribuída pelo usuário, `ID1`, a uma VM chamada *myVM* no grupo de recursos *myResourceGroup*.  Substitua `<ACCESS TOKEN>` pelo valor recebido na etapa anterior quando você solicitou um token de acesso de portador e o valor de `<SUBSCRIPTION ID>` apropriado para seu ambiente.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

5. Se houver identidades atribuídas pelo usuário ou pelo sistema à VM, você precisará adicionar a nova identidade atribuída pelo usuário à matriz `identityIDs`, retendo as identidades atribuídas pelo usuário e pelo sistema à VM no momento.

   Por exemplo, se houver uma identidade atribuída pelo sistema e uma identidade atribuída pelo usuário `ID1` à VM no momento e você desejar adicionar a identidade do usuário `ID2` a ela, use o comando CURL a seguir. Substitua `<ACCESS TOKEN>` pelo valor recebido nas etapas anteriores quando você solicitou um token de acesso de portador e o valor de `<SUBSCRIPTION ID>` apropriado para seu ambiente.

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/TestVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vm"></a>Remover uma identidade atribuída pelo usuário em uma VM do Azure

1. Recupere um token de acesso de portador, que será usado na próxima etapa no cabeçalho de autorização para criar a VM com uma identidade gerenciada atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Para garantir que você não exclua nenhuma identidade gerenciada atribuída pelo usuário existente que você deseja manter atribuída à VM nem remova a identidade atribuída pelo sistema, será necessário listar as identidades gerenciadas usando o comando CURL a seguir: 
 
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   Se houver identidades gerenciadas atribuídas à VM, elas serão listadas na resposta no valor `identity`.

   Por exemplo, se as identidades atribuídas pelo usuário `ID1` e `ID2` estiverem atribuídas à VM e você quiser manter apenas a `ID1` atribuída e reter a identidade atribuída pelo sistema, use o mesmo comando CURL que é usado para atribuir uma identidade gerenciada atribuída pelo usuário a uma VM, mantendo apenas o valor `ID1` e mantenha o valor `SystemAssigned`. Isso remove a identidade atribuída pelo usuário `ID2` da VM, retendo a identidade atribuída pelo sistema.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

Se a VM tiver identidades atribuídas pelo sistema e pelo usuário, você poderá remover todas as identidades atribuídas pelo usuário, optando por usar somente as atribuídas pelo sistema, usando o seguinte comando:

```bash
curl 'https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/TestVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```
    
Se a VM tiver apenas identidades atribuídas pelo usuário e você quiser remover todas elas, use o seguinte comando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```
## <a name="next-steps"></a>Próximas etapas

Para obter informações de como criar, listar ou excluir as atribuídas pelo usuário usando REST, confira:

- [Criar, listar ou excluir uma identidade atribuída pelo usuário usando chamadas à API REST](how-to-manage-ua-identity-rest.md)