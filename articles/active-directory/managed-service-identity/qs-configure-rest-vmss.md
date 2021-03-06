---
title: Como configurar identidades atribuídas pelo sistema e pelo usuário em um VMSS do Azure usando REST
description: Instruções passo a passo para configurar identidades atribuídas pelo usuário e pelo sistema em um VMSS do Azure usando o CURL para fazer chamadas à API REST.
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
ms.openlocfilehash: c4ccd75f679e58dd42f136bf1199eda88dc3f087
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431708"
---
# <a name="configure-managed-identity-on-a-virtual-machine-scale-set-using-rest-api-calls"></a>Configurar identidades gerenciadas em um conjunto de dimensionamento de máquinas virtuais usando chamadas à API REST

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

A Identidade Gerenciada fornece aos serviços do Azure uma identidade de sistema gerenciada automaticamente no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, você aprenderá como realizar as seguintes operações de identidade gerenciada em um conjunto de dimensionamento de máquinas virtuais, usando o CURL para fazer chamadas ao ponto de extremidade REST do Azure Resource Manager:

- Habilitar e desabilitar a identidade atribuída pelo sistema em um conjunto de dimensionamento de máquinas virtuais do Azure
- Adicionar e remover uma identidade atribuída pelo usuário em um conjunto de dimensionamento de máquinas virtuais do Azure

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com a Identidade de Serviço Gerenciada, consulte a [seção de visão geral](overview.md). **Verifique se examinou a [diferença entre uma identidade atribuída pelo sistema e uma atribuída pelo usuário](overview.md#how-does-it-work)**.
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para realizar as operações de gerenciamento deste artigo, a conta precisará das seguintes atribuições de função:
    - [Colaborador da Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) para criar um conjunto de dimensionamento de máquinas virtuais e habilitar e remover identidade gerenciada atribuída ao usuário e/ou sistema de um conjunto de dimensionamento de máquinas virtuais.
    - [Colaborador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) para criar uma identidade atribuída ao usuário.
    - Função de [Operador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator) para atribuir e remover uma identidade atribuída ao usuário de e para um conjunto de dimensionamento de máquinas virtuais.
- Se você estiver usando o Windows, instale o [subsistema do Windows para Linux](https://msdn.microsoft.com/commandline/wsl/about) ou use o [Azure Cloud Shell](../../cloud-shell/overview.md) no portal do Azure.
- Se você usa o [subsistema do Windows para Linux](https://msdn.microsoft.com/commandline/wsl/about) ou um [SO de distribuição do Linux](/cli/azure/install-azure-cli-apt?view=azure-cli-latest), [instale o console local da CLI do Azure](/azure/install-azure-cli).
- Se você estiver usando o console local da CLI do Azure, entre no Azure usando `az login` com uma conta associada à assinatura do Azure que você deseja para gerenciar as identidades atribuídas pelo sistema ou pelo usuário.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>Identidade atribuída pelo sistema

Nesta seção, você aprenderá como habilitar e desabilitar a identidade atribuída pelo sistema em um conjunto de dimensionamento de máquinas virtuais usando o CURL para fazer chamadas ao ponto de extremidade REST do Azure Resource Manager.

### <a name="enable-system-assigned-identity-during-creation-of-a-virtual-machine-scale-set"></a>Habilitar a identidade atribuída pelo sistema durante a criação de um conjunto de dimensionamento de máquinas virtuais do Azure

Para criar um conjunto de dimensionamento de máquinas virtuais com uma identidade atribuída pelo sistema habilitada, você precisa criar um conjunto de dimensionamento de máquinas virtuais e recuperar um token de acesso para usar o CURL para chamar o ponto de extremidade do Resource Manager com o valor do tipo de identidade atribuído pelo sistema.

1. Crie um [grupo de recursos](../../azure-resource-manager/resource-group-overview.md#terminology) para confinamento e implantação do conjunto de dimensionamento de máquinas virtuais e os recursos relacionados, usando [az group create](/cli/azure/group/#az-group-create). Ignore esta etapa, se você já tiver o grupo de recursos que deseja usar:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Crie uma [interface de rede](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) para seu conjunto de dimensionamento de máquinas virtuais:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Recupere um token de acesso de portador, que será usado na próxima etapa no cabeçalho de autorização para criar o conjunto de dimensionamento de máquinas virtuais com uma identidade gerenciada atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Crie um conjunto de dimensionamento de máquinas virtuais usando o CURL para chamar o ponto de extremidade REST do Azure Resource Manager. O exemplo a seguir cria um conjunto de dimensionamento de máquinas virtuais chamado *myVMSS* no *myResourceGroup* com uma identidade atribuída pelo sistema, identificada no corpo da solicitação pelo valor `"identity":{"type":"SystemAssigned"}`. Substitua `<ACCESS TOKEN>` pelo valor recebido na etapa anterior quando você solicitou um token de acesso de portador e o valor de `<SUBSCRIPTION ID>` apropriado para seu ambiente.
 
    ```bash   
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"SystemAssigned"},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
    ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Ativar identidade atribuída ao sistema em um conjunto de dimensionamento de máquina virtual do Azure existente

Para habilitar a identidade atribuída pelo sistema em um conjunto de dimensionamento de máquinas virtuais existente, você precisa adquirir um token de acesso e, em seguida, usar o CURL para chamar o ponto de extremidade REST do Resource Manager para atualizar o tipo de identidade.

1. Recupere um token de acesso de portador, que será usado na próxima etapa no cabeçalho de autorização para criar o conjunto de dimensionamento de máquinas virtuais com uma identidade gerenciada atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Use o seguinte comando CURL para chamar o ponto de extremidade REST do Azure Resource Manager para habilitar a identidade atribuída pelo sistema no conjunto de dimensionamento de máquinas virtuais, identificada no corpo da solicitação pelo valor `{"identity":{"type":"SystemAssigned"}`, para um conjunto de dimensionamento de máquinas virtuais chamado *myVMSS*.  Substitua `<ACCESS TOKEN>` pelo valor recebido na etapa anterior quando você solicitou um token de acesso de portador e o valor de `<SUBSCRIPTION ID>` apropriado para seu ambiente.
   
   > [!IMPORTANT]
   > Para garantir que você não exclua nenhuma identidade gerenciada atribuída pelo usuário existente que esteja atribuída ao conjunto de dimensionamento de máquinas virtuais, será necessário listar as identidades atribuídas pelo usuário usando este comando CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Se houver alguma identidade atribuída pelo usuário ao conjunto de dimensionamento de máquinas virtuais identificada no valor `identity`, passe para a etapa 3 que mostra como reter identidades atribuídas pelo usuário ao habilitar a identidade atribuída pelo sistema no conjunto de dimensionamento de máquinas virtuais.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Para habilitar a identidade atribuída pelo sistema em um conjunto de dimensionamento de máquinas virtuais com identidades atribuídas pelo usuário existentes, você precisará adicionar `SystemAssigned` ao valor `type`.  
   
   Por exemplo, se o conjunto de dimensionamento de máquinas virtuais tiver as identidades atribuídas pelo usuário `ID1` e `ID2` atribuídas a ele, e você desejar adicionar uma identidade atribuída pelo sistema ao conjunto de dimensionamento de máquinas virtuais, use a seguinte chamada do CURL. Substitua `<ACCESS TOKEN>` e `<SUBSCRIPTION ID>` pelos valores apropriados para seu ambiente.
   
   ```bash
   curl -v 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="disable-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Desabilitar a identidade do sistema atribuído de um conjunto de escala de máquina virtual do Azure

Para desabilitar uma identidade atribuída pelo sistema em um conjunto de dimensionamento de máquinas virtuais existente, você precisará adquirir um token de acesso e, em seguida, usar o CURL para chamar o ponto de extremidade REST do Resource Manager para atualizar o tipo de identidade para `None`.

1. Recupere um token de acesso de portador, que será usado na próxima etapa no cabeçalho de autorização para criar o conjunto de dimensionamento de máquinas virtuais com uma identidade gerenciada atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Atualize o conjunto de dimensionamento de máquinas virtuais usando o CURL para chamar o ponto de extremidade REST do Azure Resource Manager para desabilitar a identidade atribuída pelo sistema.  O exemplo a seguir desabilita a identidade atribuída pelo sistema, identificada no corpo da solicitação pelo valor `{"identity":{"type":"None"}}`, de um conjunto de dimensionamento de máquinas virtuais chamado *myVMSS*.  Substitua `<ACCESS TOKEN>` pelo valor recebido na etapa anterior quando você solicitou um token de acesso de portador e o valor de `<SUBSCRIPTION ID>` apropriado para seu ambiente.

   > [!IMPORTANT]
   > Para garantir que você não exclua nenhuma identidade gerenciada atribuída pelo usuário existente que esteja atribuída ao conjunto de dimensionamento de máquinas virtuais, será necessário listar as identidades atribuídas pelo usuário usando este comando CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Se houver alguma identidade atribuída pelo usuário ao conjunto de dimensionamento de máquinas virtuais, passe para a etapa 3 que mostra como reter identidades atribuídas pelo usuário ao remover a identidade atribuída pelo sistema do conjunto de dimensionamento de máquinas virtuais.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Para remover a identidade atribuída pelo sistema de um conjunto de dimensionamento de máquinas virtuais que tenha identidades atribuídas pelo usuário, remova `SystemAssigned` do valor `{"identity":{"type:" "}}` e mantenha o valor `UserAssigned` e a matriz `identityIds`, que definem quais identidades atribuídas pelo usuário são atribuídas ao conjunto de dimensionamento de máquinas virtuais.

## <a name="user-assigned-identity"></a>Identidade atribuída pelo usuário

Nesta seção, você aprenderá como adicionar e remover uma identidade atribuída pelo usuário em um conjunto de dimensionamento de máquinas virtuais usando o CURL para fazer chamadas ao ponto de extremidade REST do Azure Resource Manager.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Atribuir uma identidade atribuída pelo usuário durante a criação de um conjunto de dimensionamento de máquinas virtuais

1. Recupere um token de acesso de portador, que será usado na próxima etapa no cabeçalho de autorização para criar o conjunto de dimensionamento de máquinas virtuais com uma identidade gerenciada atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Crie uma [interface de rede](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) para seu conjunto de dimensionamento de máquinas virtuais:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Recupere um token de acesso de portador, que será usado na próxima etapa no cabeçalho de autorização para criar o conjunto de dimensionamento de máquinas virtuais com uma identidade gerenciada atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Crie uma identidade atribuída pelo usuário usando as instruções encontradas aqui: [Criar uma identidade gerenciada atribuída pelo usuário](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Crie um conjunto de dimensionamento de máquinas virtuais usando o CURL para chamar o ponto de extremidade REST do Azure Resource Manager. O exemplo a seguir cria um conjunto de dimensionamento de máquinas virtuais chamado *myVMSS* no grupo de recursos *myResourceGroup* com uma identidade atribuída pelo usuário `ID1`, identificada no corpo da solicitação pelo valor `"identity":{"type":"UserAssigned"}`. Substitua `<ACCESS TOKEN>` pelo valor recebido na etapa anterior quando você solicitou um token de acesso de portador e o valor de `<SUBSCRIPTION ID>` apropriado para seu ambiente.
 
   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus",{"identity":{"type":"UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Atribuir uma identidade atribuída pelo usuário em um conjunto de dimensionamento de máquinas virtuais do Azure existente

1. Recupere um token de acesso de portador, que será usado na próxima etapa no cabeçalho de autorização para criar o conjunto de dimensionamento de máquinas virtuais com uma identidade gerenciada atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Crie uma identidade atribuída pelo usuário usando as instruções encontradas aqui: [Criar uma identidade gerenciada atribuída pelo usuário](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. Para garantir que você não exclua nenhuma identidade gerenciada atribuída pelo usuário ou pelo sistema existente que esteja atribuída ao conjunto de dimensionamento de máquinas virtuais, será necessário listar os tipos de identidades atribuídos ao conjunto de dimensionamento de máquinas virtuais usando o comando CURL a seguir. Se houver identidades gerenciadas atribuídas ao conjunto de dimensionamento de máquinas virtuais, elas serão listadas no valor `identity`.
 
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

4. Se não houver nenhuma identidade atribuída pelo usuário ou pelo sistema ao conjunto de dimensionamento de máquinas virtuais, use o seguinte comando CURL para chamar o ponto de extremidade REST do Azure Resource Manager para atribuir a primeira identidade atribuída pelo usuário ao conjunto de dimensionamento de máquinas virtuais.  Se houver alguma identidade atribuída pelo usuário ou pelo sistema ao conjunto de dimensionamento de máquinas virtuais, passe para a etapa 5 que mostra como adicionar várias identidades atribuídas pelo usuário a um conjunto de dimensionamento de máquinas virtuais, mantendo a identidade atribuída pelo sistema.

   O exemplo a seguir atribui uma identidade atribuída pelo usuário, `ID1`, a um conjunto de dimensionamento de máquinas virtuais chamado *myVMSS* no grupo de recursos *myResourceGroup*.  Substitua `<ACCESS TOKEN>` pelo valor recebido na etapa anterior quando você solicitou um token de acesso de portador e o valor de `<SUBSCRIPTION ID>` apropriado para seu ambiente.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

5. Se houver identidades atribuídas pelo usuário ou pelo sistema ao conjunto de dimensionamento de máquinas virtuais, você precisará adicionar a nova identidade atribuída pelo usuário à matriz `identityIDs`, retendo as identidades atribuídas pelo usuário e pelo sistema ao conjunto de dimensionamento de máquinas virtuais no momento.

   Por exemplo, se houver uma identidade atribuída pelo sistema e uma identidade atribuída pelo usuário `ID1` ao conjunto de dimensionamento de máquinas virtuais no momento e você desejar adicionar a identidade do usuário `ID2` a ele, use o comando CURL a seguir. Substitua `<ACCESS TOKEN>` pelo valor recebido nas etapas anteriores quando você solicitou um token de acesso de portador e o valor de `<SUBSCRIPTION ID>` apropriado para seu ambiente.

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="remove-a-user-assigned-identity-from-a-virtual-machine-scale-set"></a>Remover uma identidade atribuída pelo usuário de um conjunto de dimensionamento de máquinas virtuais

1. Recupere um token de acesso de portador, que será usado na próxima etapa no cabeçalho de autorização para criar o conjunto de dimensionamento de máquinas virtuais com uma identidade gerenciada atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Para garantir que você não exclua nenhuma identidade gerenciada atribuída pelo usuário existente que você deseja manter atribuída ao conjunto de dimensionamento de máquinas virtuais nem remova a identidade atribuída pelo sistema, será necessário listar as identidades gerenciadas usando o comando CURL a seguir: 
   
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```
   
   Se houver identidades gerenciadas atribuídas à VM, elas serão listadas na resposta no valor `identity`. 
    
   Por exemplo, se as identidades atribuídas pelo usuário `ID1` e `ID2` estiverem atribuídas ao conjunto de dimensionamento de máquinas virtuais e você quiser manter apenas a `ID1` atribuída e reter a identidade atribuída pelo sistema, use o mesmo comando CURL que é usado para atribuir uma identidade gerenciada atribuída pelo usuário a um conjunto de dimensionamento de máquinas virtuais, mantendo apenas o valor `ID1` e mantenha o valor `SystemAssigned`. Isso remove a identidade atribuída pelo usuário `ID2` do conjunto de dimensionamento de máquinas virtuais, retendo a identidade atribuída pelo sistema.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

Se o conjunto de dimensionamento de máquinas virtuais tiver identidades atribuídas pelo sistema e atribuídas pelo usuário, você poderá remover todas as identidades atribuídas pelo usuário, optando por usar somente as atribuídas pelo sistema usando o seguinte comando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```
    
Se o conjunto de dimensionamento de máquinas virtuais tiver apenas identidades atribuídas pelo usuário e você quiser remover todas elas, use o seguinte comando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

## <a name="next-steps"></a>Próximas etapas

Para obter informações de como criar, listar ou excluir as atribuídas pelo usuário usando REST, confira:

- [Criar, listar ou excluir uma identidade atribuída pelo usuário usando chamadas à API REST](how-to-manage-ua-identity-rest.md)