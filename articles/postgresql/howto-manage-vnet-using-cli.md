---
title: Criar e gerenciar regras e pontos de extremidade de serviços VNET do Banco de Dados do Azure para PostgreSQL usando a CLI do Azure | Microsoft Docs
description: Este artigo descreve como criar e gerenciar regras e pontos de extremidade de serviços VNet do Banco de Dados do Azure para PostgreSQL usando a linha de comando CLI do Azure.
services: postgresql
author: mbolz
ms.author: mbolz
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/01/2018
ms.openlocfilehash: 7312000d1f22af3eb0091b46caac2c9607231513
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38531694"
---
# <a name="create-and-manage-azure-database-for-postgresql-vnet-service-endpoints-using-azure-cli"></a>Criar e gerenciar pontos de extremidade de serviços VNet do Banco de Dados do Azure para PostgreSQL usando a CLI do Azure
As regras e pontos de extremidade de serviços de VNet (rede virtual) estendem o espaço de endereço privado de uma rede virtual para seu servidor do Banco de Dados do Azure para PostgreSQL. Usando comandos convenientes da CLI (interface de linha de comando) do Azure, você pode criar, atualizar, excluir, listar e mostrar as regras e os pontos de extremidade de serviço de VNet para gerenciar o servidor. Para obter uma visão geral dos pontos de extremidade de serviço de VNet do Banco de Dados do Azure para PostgreSQL, confira [Pontos de extremidade de serviço de VNet do servidor do Banco de Dados do Azure para PostgreSQL](concepts-data-access-and-security-vnet.md). Os pontos de extremidade de serviços VNet estão disponíveis na versão prévia pública em todas as regiões compatíveis com o Banco de Dados do Azure para PostgreSQL.

## <a name="prerequisites"></a>pré-requisitos
Para seguir este guia de instruções, você precisa:
- Instalar o utilitário de linha de comando [CLI do Azure 2.0](/cli/azure/install-azure-cli) ou usar o Azure Cloud Shell no navegador.
- Um [banco de dados e servidor do Banco de Dados do Azure para PostgreSQL](quickstart-create-server-database-azure-cli.md).

> [!NOTE]
> O suporte para ponto de extremidade de serviço de VNet é apenas para servidores de Uso Geral e Otimizados para Memória.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-postgresql"></a>Configurar pontos de extremidade de serviço de VNet para Banco de Dados do Azure para PostgreSQL
Os comandos [az network vnet](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) são usados para configurar redes virtuais.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este artigo exigirá que seja executada a CLI do Azure versão 2.0 ou posterior. Para ver a versão instalada, execute o comando `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

Se você estiver executando a CLI localmente, precisará fazer logon em sua conta usando o comando [az login](/cli/azure/authenticate-azure-cli?view=interactive-log-in). Observe a propriedade **id** da saída do comando para o nome da assinatura correspondente.
```azurecli-interactive
az login
```
Instale a extensão CLI para pontos de extremidade de serviço de VNet do Banco de Dados do Azure para PostgreSQL usando o comando `az extension add --name rdbms-vnet`. 
```azurecli-interactive
az extension add --name rdbms-vnet
```

Execute o comando `az extension list` para verificar a instalação da extensão CLI.
```azurecli-interactive
az extension list
```
A saída do comando lista todas as extensões instaladas. A extensão CLI do Banco de Dados do Azure para PostgreSQL é:

 { "extensionType": "whl", "name": "rdbms-vnet", "version": "10.0.0" }

> [!NOTE]
> Para desinstalar a extensão CLI, execute o comando `az extension remove -n rdbms-vnet`. 

Se tiver várias assinaturas, escolha a que for adequada para cobrança do recurso. Selecione a ID da assinatura específica em sua conta usando o comando [az account set](/cli/azure/account#az_account_set). Substitua a propriedade **id** da saída **logon az** para a sua assinatura no espaço reservado da ID da assinatura.

- A conta deve ter as permissões necessárias para criar uma rede virtual e um ponto de extremidade de serviço.

Pontos de extremidade de serviço podem ser configurados em redes virtuais de forma independente por um usuário com acesso de gravação à rede virtual.

Para garantir recursos de serviço do Azure para uma rede virtual, o usuário deve ter permissão "Microsoft.Network/JoinServicetoaSubnet" para as sub-redes que estão sendo adicionadas. Essa permissão está incluída nas funções de administrador de serviço internas por padrão e pode ser modificada com a criação de funções personalizadas.

Saiba mais sobre [funções internas](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) e como atribuir permissões específicas a [funções personalizadas](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).

As VNets e os recursos de serviço do Azure podem estar na mesma assinatura ou em assinaturas diferentes. Se os recursos de serviço da rede virtual e do Azure estão em assinaturas diferentes, os recursos devem estar no mesmo locatário do AD (Active Directory) no momento dessa versão prévia.

> [!IMPORTANT]
> É altamente recomendável ler este artigo sobre considerações e configurações de ponto de extremidade de serviço antes de executar o script de exemplo abaixo ou configurar pontos de extremidade de serviço. **Ponto de extremidade de serviço de rede virtual:** um [ponto de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md) é uma sub-rede cujos valores de propriedade incluem um ou mais nomes de tipo formais de serviço do Azure. Os pontos de extremidade de serviços de VNet usam o nome de tipo de serviço **Microsoft.Sql**, que se refere ao serviço do Azure chamado Banco de Dados SQL. Essa marcação de serviço também é aplicável aos serviços Banco de Dados SQL do Azure, Banco de Dados do Azure para PostgreSQL e MySQL. É importante observar que, ao aplicar a marcação de serviço **Microsoft.Sql** a um ponto de extremidade de serviço de VNet, ela configura o tráfego do ponto de extremidade de serviço para todos os serviços de Banco de Dados do Azure, incluindo servidores do Banco de Dados SQL do Azure, do Banco de Dados do Azure para PostgreSQL e do Banco de Dados do Azure para MySQL na sub-rede. 
> 

### <a name="sample-script-to-create-an-azure-database-for-postgresql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Exemplo de script para criar um Banco de Dados do Azure para PostgreSQL, uma VNet, um ponto de extremidade de serviço de VNet e proteger o servidor da sub-rede com uma regra de VNet
Neste script de exemplo, altere as linhas destacadas para personalizar o nome de usuário administrador e a senha. Substitua a SubscriptionID usada nos comandos `az account set --subscription` pelo identificador de assinatura.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/create-postgresql-server.sh?highlight=5,20 "Create an Azure Database for PostgreSQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Limpar a implantação
Após a execução do script de exemplo, o comando a seguir pode ser usado para remover o grupo de recursos e todos os recursos associados a ele.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/delete-postgresql.sh "Delete the resource group.")]
