---
title: Implantar grupos com vários contêineres em Instâncias de Contêiner do Azure
description: Saiba como implantar um grupo de contêiner com vários contêineres em Instâncias de Contêiner do Azure.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 8a3dbe67a835cc9f1b8deca6a103e3f4aaa66ff9
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39424664"
---
# <a name="deploy-a-container-group"></a>Implantar um grupo de contêineres

As Instâncias de Contêiner do Azure são compatíveis com a implantação de vários contêineres em um único host utilizando um [grupo de contêineres](container-instances-container-groups.md). Isso é útil ao criar um aplicativo secundário para registro em log, monitoramento ou qualquer outra configuração em que um serviço precise de um segundo processo anexado.

Há dois métodos para implantar o contêiner com vários grupos usando a CLI do Azure:

* Implantação de modelo do Resource Manager (este artigo)
* [Implantação do arquivo YAML](container-instances-multi-container-yaml.md)

A implantação com um modelo do Resource Manager será recomendável se você precisar implantar recursos adicionais de serviço do Azure (por exemplo, um compartilhamento de Arquivos do Azure) no momento da implantação da instância de contêiner. Devido à natureza mais concisa do formato YAML, recomendamos a implantação com um arquivo YAML quando sua implantação incluir *somente* instâncias de contêiner.

> [!NOTE]
> Grupos com vários contêineres são atualmente restritos a contêineres do Linux. Enquanto estamos trabalhando para trazer todos os recursos para contêineres do Windows, você pode encontrar as diferenças atuais de plataforma em [Cotas e disponibilidade de região para Instâncias de Contêiner do Azure](container-instances-quotas.md).

## <a name="configure-the-template"></a>Configurar o modelo

As seções deste artigo explicam passo a passo como executar uma configuração de sidecar simples de vários contêineres implantando um modelo do Azure Resource Manager.

Crie um arquivo chamado `azuredeploy.json` e copie o seguinte JSON para ele.

Este modelo do Resource Manager define um grupo de contêineres com dois contêineres, um endereço IP público e duas portas expostas. O primeiro contêiner no grupo executa um aplicativo voltado para a Internet. O outro contêiner, o secundário, faz uma solicitação HTTP para o aplicativo Web principal por meio da rede local do grupo.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "microsoft/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "microsoft/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2018-04-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            },
            {
                "protocol": "tcp",
                "port": "8080"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

Para usar um registro de imagem de contêiner privado, adicione um objeto ao documento JSON com o formato a seguir. Para um exemplo de implementação dessa configuração, consulte a documentação [Referência de modelo do Gerenciador de recursos de ACI] [ template-reference].

```JSON
"imageRegistryCredentials": [
  {
    "server": "[parameters('imageRegistryLoginServer')]",
    "username": "[parameters('imageRegistryUsername')]",
    "password": "[parameters('imageRegistryPassword')]"
  }
]
```

## <a name="deploy-the-template"></a>Implantar o modelo

Crie um grupo de recursos com o comando [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Implante o modelo com o comando [az group deployment create][az-group-deployment-create].

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file azuredeploy.json
```

Em alguns segundos, você deverá receber uma resposta inicial do Azure.

## <a name="view-deployment-state"></a>Exibir estado da implantação

Para exibir o estado da implantação, use o comando [az container show][az-container-show] a seguir:

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Se quiser exibir o aplicativo em execução, navegue até o endereço IP dele em seu navegador. Por exemplo, o IP é `52.168.26.124` nesta saída de exemplo:

```bash
Name              ResourceGroup    ProvisioningState    Image                                                           IP:ports               CPU/Memory       OsType    Location
----------------  ---------------  -------------------  --------------------------------------------------------------  ---------------------  ---------------  --------  ----------
myContainerGroup  myResourceGroup  Succeeded            microsoft/aci-helloworld:latest,microsoft/aci-tutorial-sidecar  52.168.26.124:80,8080  1.0 core/1.5 gb  Linux     westus
```

## <a name="view-logs"></a>Exibir logs

Veja a saída de log de um contêiner usando o comando [az container logs][az-container-logs]. O argumento `--container-name` especifica o contêiner do qual efetuar pull dos logs. Neste exemplo, é especificado o primeiro contêiner.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Saída:

```bash
listening on port 80
::1 - - [09/Jan/2018:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [09/Jan/2018:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [09/Jan/2018:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Para ver os logs do contêiner secundário, execute o mesmo comando, especificando o nome do segundo contêiner.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Saída:

```bash
Every 3s: curl -I http://localhost                          2018-01-09 23:25:11

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
X-Powered-By: Express
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Wed, 29 Nov 2017 06:40:40 GMT
ETag: W/"67f-16006818640"
Content-Type: text/html; charset=UTF-8
Content-Length: 1663
Date: Tue, 09 Jan 2018 23:25:11 GMT
Connection: keep-alive
```

Como você pode ver, o secundário está periodicamente fazendo uma solicitação HTTP ao aplicativo Web principal por meio da rede local do grupo a fim de garantir que ele esteja em execução. Este exemplo secundário pode ser expandido para disparar um alerta, caso receba um código de resposta HTTP diferente de 200 OK.

## <a name="next-steps"></a>Próximas etapas

Este artigo abordou as etapas necessárias para implantar uma instância de contêiner do Azure de vários contêineres. Para uma experiência de ponta a ponta de Instâncias de Contêiner do Azure, consulte o tutorial de Instâncias de Contêiner do Azure.

> [!div class="nextstepaction"]
> [Tutorial sobre Instâncias de Contêiner do Azure][aci-tutorial]

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
