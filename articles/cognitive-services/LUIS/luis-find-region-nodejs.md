---
title: Localize a região do LUIS com Node.js nos limites de Reconhecimento vocal (LUIS) | Microsoft Docs
description: Programaticamente, localize a região de publicação com chave de ponto de extremidade e ID de aplicativo para LUIS.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/01/2018
ms.author: diberry
ms.openlocfilehash: 66a34b5b611934ca531e7709b13e0c6d1c794206
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39222504"
---
# <a name="region-can-be-determined-from-api-call"></a>A região pode ser determinada da chamada à API 
Se você tiver a ID do aplicativo LUIS e a ID da assinatura do LUIS, poderá encontrar a região que será usada para consultas de ponto de extremidade.

> [!NOTE] 
> A solução Node.js completa está disponível nos [**exemplos de LUIS** no repositório Github](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/find-region/nodejs/).

## <a name="luis-endpoint-query-strategy"></a>Estratégia de consulta de ponto de extremidade do LUIS
Cada consulta de ponto de extremidade do LUIS exige:

* Uma chave de ponto de extremidade
* Uma ID do aplicativo
* Uma região

Se a consulta do ponto de extremidade de LUIS usar a chave do ponto de extremidade e a ID do aplicativo corretos, mas a região errada, o código de resposta será 401. A solicitação 401 não é considerada na cota da assinatura. Transforme esta solicitação em uma estratégia para pesquisar todas as regiões para localizar a região correta. A região correta é a única solicitação que retorna um código de status 2xx. 

|Código de resposta|parâmetros|
|--|--|
|2xx|chave de ponto de extremidade correta<br>ID do aplicativo correta<br>região do host correta|
|401|chave de ponto de extremidade correta<br>ID do aplicativo correta<br>região do host _incorreta_|

## <a name="nodejs-code-to-find-region"></a>Código de classe Node.js para localizar a região
O aplicativo de console usa a ID do aplicativo do LUIS e a chave de ponto de extremidade e retorna todas as regiões associadas a ele. Atualmente, uma chave de ponto de extremidade é criada por região, de modo que apenas uma região deve retornar.

Inclua as dependências de NPM:

[!code-javascript[Add the dependencies](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=5-6 "Add the dependencies")]

Adicione constantes. Substitua os valores de variável para `subscriptionKey` e `appId` pelos seus próprios valores.  

[!code-javascript[Add constants](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=8-25 "Add constants")]

Adicione a função `searchRegions` para localizar a região. Todas as regiões incorretas retornam 401, que é capturado e ignorado.

[!code-javascript[Find region](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=27-37 "Find region")]

Chame a função `searchRegions` e retorne uma única região:

[!code-javascript[Call the function](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=39-43 "Call the function")]

Quando o aplicativo é executado, o terminal mostra a região da ID do aplicativo.

![Captura de tela do aplicativo de console mostrando a região do LUIS](./media/find-region-nodejs/console.png)


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as [regiões](luis-reference-regions.md) do LUIS.
