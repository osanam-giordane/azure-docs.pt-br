---
title: Tutorial do Kubernetes no Azure – Implantar aplicativo
description: Tutorial de AKS – implantar aplicativo
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: e0e349361afaac9aec816d7f5d158322d6f4e691
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37100907"
---
# <a name="tutorial-run-applications-in-azure-kubernetes-service-aks"></a>Tutorial: executar aplicativos no AKS (Serviço de Kubernetes do Azure)

Neste tutorial, parte quatro de sete, um aplicativo de exemplo é implantado em um cluster Kubernetes. As etapas concluídas incluem:

> [!div class="checklist"]
> * Atualizar os arquivos de manifesto do Kubernetes
> * Executar um aplicativo no Kubernetes
> * Testar o aplicativo

Nos tutoriais subsequentes, esse aplicativo é escalado horizontalmente e atualizado.

Este tutorial assume uma compreensão básica dos conceitos de Kubernetes; para obter informações detalhadas sobre Kubernetes consulte a [documentação do Kubernetes][kubernetes-documentation].

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, um aplicativo foi empacotado em uma imagem de contêiner, essa imagem foi carregada no Registro de Contêiner do Azure e um cluster Kubernetes foi criado.

Para concluir este tutorial, você precisa do arquivo de manifesto do Kubernetes `azure-vote-all-in-one-redis.yaml` pré-criado. Esse arquivo foi baixado com o código-fonte do aplicativo em um tutorial anterior. Verifique se você clonou repositório e se você alterou os diretórios para o repositório clonado.

Se você ainda não realizou essas etapas e deseja continuar acompanhando, retorne ao [Tutorial 1 – Criar imagens de contêiner][aks-tutorial-prepare-app].

## <a name="update-manifest-file"></a>Atualizar arquivo de manifesto

Neste tutorial, o ACR (Registro de Contêiner do Azure) foi usado para armazenar uma imagem de contêiner. Antes de executar o aplicativo, o nome do servidor de logon do ACR precisa ser atualizado no arquivo de manifesto do Kubernetes.

Obter o nome do servidor de logon ACR com o comando [az acr list][az-acr-list].

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

O arquivo de manifesto foi pré-criado com um nome do servidor de logon `microsoft`. Abra o arquivo com qualquer editor de texto. Neste exemplo, o arquivo é aberto com o `nano`.

```console
nano azure-vote-all-in-one-redis.yaml
```

Substitua `microsoft` pelo nome do servidor de logon do ACR. Esse valor pode ser encontrado na linha **47** do arquivo de manifesto.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Em seguida, torna-se o código acima.

```yaml
containers:
- name: azure-vote-front
  image: <acrName>.azurecr.io/azure-vote-front:v1
```

Salve e feche o arquivo.

## <a name="deploy-application"></a>Implantar um aplicativo

Use o comando [kubectl apply][kubectl-apply] para executar o aplicativo. Esse comando analisa o arquivo de manifesto e cria objetos Kubernetes definidos.

```azurecli
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

Saída:

```
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>Testar aplicativo

Um [serviço Kubernetes][kubernetes-service] é criado que expõe o aplicativo para a internet. Esse processo pode levar alguns minutos.

Para monitorar o andamento, use o comando [kubectl get service][kubectl-get] com o argumento `--watch`.

```azurecli
kubectl get service azure-vote-front --watch
```

Inicialmente o *EXTERNAL -IP* para o serviço *azure-vote-front* aparece como *pendente*.

```
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
```

Depois que o endereço *EXTERNAL-IP* for alterado de *pendente* para um *endereço IP*, use `CTRL-C` para interromper o processo kubectl watch.

```
azure-vote-front   10.0.34.242   52.179.23.131   80:30676/TCP   2m
```

Para consultar o aplicativo, navegue até o endereço IP externo.

![Imagem do cluster Kubernetes no Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

Se o aplicativo não foi carregado, ele pode ser devido a um problema de autorização com o registro da imagem.

Siga estas etapas para [permitir acesso por meio de um segredo de Kubernetes](https://docs.microsoft.com/azure/container-registry/container-registry-auth-aks#access-with-kubernetes-secret).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, o aplicativo Azure vote foi implantado em um cluster Kubernetes no AKS. As tarefas concluídas incluem:

> [!div class="checklist"]
> * Baixar arquivos de manifesto Kubernetes
> * Executar um aplicativo no Kubernetes
> * Testado o aplicativo

Avance para o próximo tutorial para saber mais sobre como dimensionar um aplicativo Kubernetes e a infraestrutura do Kubernetes subjacente.

> [!div class="nextstepaction"]
> [Dimensionar um aplicativo do Kubernetes e sua infraestrutura][aks-tutorial-scale]

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-documentation]: https://kubernetes.io/docs/home/
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[az-acr-list]: /cli/azure/acr#list
