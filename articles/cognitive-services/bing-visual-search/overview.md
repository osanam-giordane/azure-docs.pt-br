---
title: Visão geral da API de Pesquisa Visual do Bing | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Mostra como obter detalhes ou insights sobre uma imagem, como imagens semelhantes ou fontes de compra.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 04/10/2018
ms.author: scottwhi
ms.openlocfilehash: aa563d89b1834f5be952f13c31a2451d809709b1
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39006522"
---
# <a name="what-is-bing-visual-search-api"></a>O que é a API da Pesquisa Visual do Bing?

A API de Pesquisa Visual do Bing fornece uma experiência semelhante aos detalhes da imagem mostrados em Bing.com/images. Com a Pesquisa Visual, você pode fazer upload de uma imagem e obter insights sobre a imagem, como imagens visualmente semelhantes, fontes de compra, páginas da Web que incluem a imagem e muito mais. Em vez de fazer uploado de uma imagem, você também pode fornecer um token de insights obtido de uma imagem nos resultados da pesquisa de imagens (confira [API de Imagens do Bing](../bing-image-search/overview.md)).

A Pesquisa Visual pode identificar celebridades, monumentos e pontos de referência, obras de arte, móveis para casa, artigos de moda, produtos, OCR (reconhecimento de caracteres) e muito mais.

Veja a seguir a descoberta de insights possibilitada pela Pesquisa Visual.

- Imagens visualmente semelhantes – uma lista de imagens que são visualmente semelhantes à imagem de entrada
- Produtos visualmente semelhantes – uma lista de imagens que contêm os produtos que são visualmente semelhantes ao produto mostrado na imagem de entrada
- Fontes de compra – uma lista dos locais em que você pode comprar o item mostrado na imagem de entrada
- Pesquisas relacionadas – uma lista de pesquisas relacionadas feitas por outras pessoas ou que se baseiam no conteúdo da imagem
- Páginas da Web que incluem a imagem – uma lista das páginas da Web que incluem a imagem de entrada
- Receitas – uma lista das páginas da Web que incluem receitas para preparação do prato mostrado na imagem de entrada

Além desses insights, a Pesquisa Visual também retorna um conjunto diversificado de termos (marcas) derivados da imagem de entrada. Essas marcas permitem aos usuários explorar os conceitos encontrados na imagem. Por exemplo, se a imagem de entrada for a de um atleta famoso, uma das marcações poderá ser o nome do atleta e a outra poderá ser Esportes. Ou se a imagem de entrada for a de uma torta de maçã, as marcas poderão ser Torta de Maçã, Tortas, Sobremesas, de modo que os usuários possam explorar conceitos relacionados.

Os resultados da Pesquisa Visual também incluem caixas delimitadoras para as áreas de interesse na imagem. Por exemplo, se a imagem contiver várias celebridades, os resultados poderão incluir caixas delimitadoras para cada uma das celebridades reconhecidas na imagem. Ou se o Bing reconhecer um produto ou um item de vestuário na imagem, o resultado poderá incluir uma caixa delimitadora para o produto ou o item de vestuário reconhecido.

> [!IMPORTANT]
> Se você usar o ponto de extremidade /images/details para [obter insights de imagem](../bing-image-search/image-insights.md), deverá atualizar o código para usar a Pesquisa Visual, pois ela fornece insights mais abrangentes.


## <a name="the-request"></a>A solicitação

Veja a seguir as opções para obter insights sobre uma imagem. 

- Enviar um token de insights obtido de uma imagem em uma chamada anterior a um dos pontos de extremidade da [API de Imagens do Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
- Enviar a URL de uma imagem
- Fazer upload de uma imagem (binário)


Se você enviar uma URL ou um token de imagem à Pesquisa Visual, inclua o objeto JSON mostrado a seguir no corpo do POST. 

```json
{
    "imageInfo" : {
        "url" : "",
        "imageInsightsToken" : "",
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.5,
            "right" : 0.9,
            "bottom" : 0.9
        }
    },
    "knowledgeRequest" : {
      "filters" : {
        "site" : ""
      }
    }
}
```

O objeto `imageInfo` deve incluir o campo `url` ou `imageInsightsToken` mas não ambos. Defina o campo `url` com a URL de uma imagem acessível pela Internet. O tamanho máximo de imagem compatível é 1 MB.

O `imageInsightsToken` precisa ser definido como um token de insights. Para obter um token de insights, chame a API de Imagem do Bing. A resposta contém uma lista de objetos `Image`. Cada objeto `Image` contém um campo `imageInsightsToken`, que contém o token.

O campo `cropArea` é opcional. A área de corte especifica a parte superior esquerda e a parte inferior direita de uma região de interesse. Especifique os valores no intervalo de 0,0 a 1,0. Os valores são um percentual da largura ou da altura geral. Por exemplo, o exemplo acima marca a metade direita da imagem como a região de interesse. Inclua-a se desejar limitar a solicitação de insights para a região de interesse.

O objeto `filters` contém um filtro de site (confira o campo `site`) que você pode usar para restringir os resultados de imagens e produtos semelhantes a um domínio específico. Por exemplo, se a imagem for de um Surface Book, você poderá definir `site` como www.microsoft.com. 

Caso deseje obter insights sobre uma cópia local de uma imagem, faça upload da imagem como dados binários.

Para obter detalhes sobre como incluir essas opções no corpo do POST, confira [Tipos de formulário de conteúdo](#content-form-types).


### <a name="endpoint"></a>Ponto de extremidade

O ponto de extremidade da Pesquisa Visual é https:\/\/api.cognitive.microsoft.com/bing/v7.0/images/visualsearch.

As solicitações precisam ser enviadas como apenas solicitações HTTP POST. 


### <a name="query-parameters"></a>Parâmetros de consulta

Veja a seguir os parâmetros de consulta que devem ser especificados pela solicitação. No mínimo, você deve incluir o parâmetro de consulta `mkt`.

|NOME|Valor|Tipo|Obrigatório|  
|----------|-----------|----------|--------------|  
|<a name="cc" />cc|O código de dois caracteres do país do qual os resultados são obtidos.<br /><br /> Se você definir esse parâmetro, também precisará especificar o cabeçalho [Accept-Language](#acceptlanguage). O Bing usa o primeiro idioma compatível encontrado na lista de idiomas e combina o idioma com o código do país especificado para determinar o mercado do qual os resultados são retornados. Se a lista de idiomas não incluir um idioma compatível, o Bing encontrará o idioma e o mercado mais próximos que dão suporte à solicitação. Ou ele pode usar um mercado agregado ou padrão para os resultados, em vez daquele especificado.<br /><br /> Você deverá usar esse parâmetro de consulta e o parâmetro de consulta `Accept-Language` somente se especificar vários idiomas; caso contrário, use os parâmetros de consulta `mkt` e `setLang`.<br /><br /> Esse parâmetro e o parâmetro de consulta [mkt](#mkt) são mutuamente exclusivos – não especifique ambos.|Cadeia de caracteres|Não |  
|<a name="mkt" />mkt|O mercado do qual os resultados são obtidos. <br /><br /> **OBSERVAÇÃO:** é recomendável sempre especificar o mercado, se conhecido. A especificação do mercado ajuda o Bing a encaminhar a solicitação e retornar uma resposta apropriada e ideal.<br /><br /> Esse parâmetro e o parâmetro de consulta [cc](#cc) são mutuamente exclusivos – não especifique ambos.|Cadeia de caracteres|SIM|  
|<a name="safesearch" />Pesquisa Segura|Um filtro usado para filtrar o conteúdo para adulto. Veja a seguir os possíveis valores de filtro que não diferenciam maiúsculas de minúsculas.<br /><ul><li>Desativado – retorna páginas da Web com texto ou imagens para adulto.<br /><br/></li><li>Moderado – retorna páginas da Web com texto para adulto, mas não imagens para adulto.<br /><br/></li><li>Estrito – não retorna páginas da Web com texto ou imagens para adulto.</li></ul><br /> O padrão é Moderado.<br /><br /> **OBSERVAÇÃO:** se a solicitação for proveniente de um mercado cuja política de conteúdo para adulto do Bing exija que `safeSearch` seja definido como Estrito, o Bing ignorará o valor `safeSearch` e usará Estrito.<br/><br/>**OBSERVAÇÃO:** se você usar o operador de consulta `site:`, há a possibilidade de que a resposta possa trazer um conteúdo para adulto, seja qual for a definição do parâmetro de consulta `safeSearch`. Só use `site:` se estiver ciente do conteúdo do site e se o cenário der suporte à possibilidade de conteúdo para adulto. |Cadeia de caracteres|Não |  
|<a name="setlang" />setLang|O idioma a ser usado para cadeias de caracteres de interface do usuário. Especifique o idioma usando o código de idioma ISO 639-1 de 2 letras. Por exemplo, o código de idioma para o inglês é EN. O padrão é EN (inglês).<br /><br /> Embora isso seja opcional, você sempre deve especificar o idioma. Normalmente, você define `setLang` com o mesmo idioma especificado por `mkt`, a menos que o usuário deseje exibir as cadeias de caracteres de interface do usuário em outro idioma.<br /><br /> Esse parâmetro e o cabeçalho [Accept-Language](#acceptlanguage) são mutuamente exclusivos – não especifique ambos.<br /><br /> Uma cadeia de caracteres de interface do usuário é uma cadeia de caracteres que é usada como um rótulo em uma interface do usuário. Há poucas cadeias de caracteres de interface do usuário nos objetos de resposta JSON. Além disso, todos os links para as propriedades de Bing.com nos objetos de resposta aplicam o idioma especificado.|Cadeia de caracteres|Não | 

### <a name="headers"></a>Cabeçalhos

Veja a seguir os cabeçalhos que devem ser especificados pela solicitação. Os cabeçalhos Content-Type e Ocp-Apim-Subscription-Key são os únicos cabeçalhos obrigatórios, mas você também deve incluir User-Agent, X-MSEdge-ClientID, X-MSEdge-ClientIP e X-Search-Location.


|Cabeçalho|DESCRIÇÃO|  
|------------|-----------------|  
|<a name="acceptlanguage" />Accept-Language|Cabeçalho de solicitação opcional.<br /><br /> Uma lista delimitada por vírgula de idiomas a serem usados para as cadeias de caracteres de interface do usuário. A lista está em ordem decrescente de preferência. Para obter mais informações, incluindo o formato esperado, confira [RFC2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Esse cabeçalho e o parâmetro de consulta [setLang](#setlang) são mutuamente exclusivos – não especifique ambos.<br /><br /> Se você definir esse cabeçalho, também deverá especificar o parâmetro de consulta [cc](#cc). Para determinar o mercado para o qual retornar os resultados, o Bing usa o primeiro idioma compatível suporte encontrado na lista e combina-o com o valor de parâmetro `cc`. Se a lista não inclui um idioma compatível, o Bing encontra o idioma e o mercado mais próximos que dão suporte à solicitação ou usa um mercado padrão ou agregado para os resultados. Para determinar o mercado usado pelo Bing, confira o cabeçalho BingAPIs-Market.<br /><br /> Use esse cabeçalho e o parâmetro de consulta `cc` somente se você especificar vários idiomas. Caso contrário, use os parâmetros de consulta [mkt](#mkt) e [setLang](#setlang).<br /><br /> Uma cadeia de caracteres de interface do usuário é uma cadeia de caracteres que é usada como um rótulo em uma interface do usuário. Há poucas cadeias de caracteres de interface do usuário nos objetos de resposta JSON. Todos os links para as propriedades de Bing.com nos objetos de resposta aplicam o idioma especificado.|  
|<a name="contenttype" />Content-Type|Cabeçalho de solicitação obrigatório.<br /><br />Precisa ser definido como multipart/form-data e incluir um parâmetro de limite (por exemplo, multipart/form-data; boundary=\<boundary string\>). Para obter mais informações, consulte [Tipos de formulário de conteúdo](#content-form-types).
|<a name="market" />BingAPIs-Market|Cabeçalho de resposta.<br /><br /> O mercado usado pela solicitação. O formulário é \<languageCode\>-\<countryCode\>. Por exemplo, en-US.|  
|<a name="traceid" />BingAPIs-TraceId|Cabeçalho de resposta.<br /><br /> A ID da entrada de log que contém os detalhes da solicitação. Quando ocorrer um erro, capture essa ID. Se você não conseguir determinar e resolver o problema, inclua essa ID juntamente com outras informações fornecidas à equipe de Suporte.|  
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Cabeçalho de solicitação obrigatório.<br /><br /> A chave de assinatura que você recebeu quando se inscreveu nesse serviço nos [Serviços Cognitivos](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Pragma|Cabeçalho de solicitação opcional<br /><br /> Por padrão, o Bing retorna o conteúdo armazenado em cache se disponível. Para impedir que o Bing retorne o conteúdo armazenado em cache, defina o cabeçalho Pragma como no-cache (por exemplo, Pragma: no-cache).
|<a name="useragent" />User-Agent|Cabeçalho de solicitação opcional.<br /><br /> O agente do usuário que originou a solicitação. O Bing usa o agente do usuário para fornecer uma experiência otimizada aos usuários móveis. Embora isso seja opcional, você é incentivado a sempre especificar esse cabeçalho.<br /><br /> O agente do usuário deve ter a mesma cadeia de caracteres enviada por qualquer navegador geralmente usado. Para obter informações sobre agentes do usuário, confira [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Veja a seguir exemplos de cadeias de caracteres de agente do usuário.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (compatível; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; como Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 como Mac OS X) AppleWebKit/536.26 (KHTML; como Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) como Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU OS 7_0 como Mac OS X) AppleWebKit/537.51.1 (KHTML, como Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|Cabeçalho de solicitação e resposta opcional.<br /><br /> O Bing usa esse cabeçalho para fornecer aos usuários um comportamento consistente nas chamadas à API do Bing. Em geral, o Bing faz o pré-lançamento de novos recursos e novas melhorias e usa a ID do cliente como uma chave para atribuição de tráfego em versões de pré-lançamento diferentes. Se você não usar a mesma ID do cliente para um usuário em várias solicitações, o Bing poderá atribuir o usuário a várias versões de pré-lançamento conflitantes. A atribuição a várias versões de pré-lançamento conflitantes pode levar a uma experiência do usuário inconsistente. Por exemplo, se a segunda solicitação tem uma atribuição de versão de pré-lançamento diferente da primeira, a experiência pode ser inesperada. Além disso, o Bing pode usar a ID do cliente para adaptar os resultados da Web ao histórico de pesquisa dessa ID do cliente, fornecendo uma experiência mais rica para o usuário.<br /><br /> O Bing também usa esse cabeçalho para ajudar a melhorar as classificações de resultado pela análise da atividade gerada por uma ID do cliente. As melhorias de relevância ajudam com uma melhor qualidade dos resultados fornecidos pelas APIs do Bing e, por sua vez, permitem taxas de clickthrough mais altas para o consumidor da API.<br /><br /> **IMPORTANTE:** embora isso seja opcional, você deve considerar esse cabeçalho obrigatório. A persistência da ID do cliente em várias solicitações para a mesma combinação de usuário final e dispositivo permite 1) ao consumidor da API receber uma experiência do usuário consistente e 2) taxas de clickthrough mais altas por meio de uma melhor qualidade dos resultados das APIs do Bing.<br /><br /> Veja a seguir as regras de uso básicas que se aplicam a esse cabeçalho.<br /><ul><li>Cada usuário que usa o aplicativo no dispositivo precisa ter uma ID do cliente exclusiva gerada pelo Bing.<br /><br/>Se você não incluir esse cabeçalho na solicitação, o Bing gerará uma ID e a retornará no cabeçalho de resposta X-MSEdge-ClientID. A única vez que você NÃO deve incluir esse cabeçalho em uma solicitação é a primeira vez que o usuário usa o aplicativo nesse dispositivo.<br /><br/></li><li>**ATENÇÃO:** garanta que essa ID do Cliente não seja vinculável aos dados da conta de usuário autenticado.</li><li>Use a ID do cliente para cada solicitação da API do Bing feita pelo aplicativo para esse usuário no dispositivo.<br /><br/></li><li>Persista a ID do cliente. Para persistir a ID em um aplicativo de navegador, use um cookie HTTP persistente para garantir que a ID seja usada em todas as sessões. Não use um cookie de sessão. Para outros aplicativos, como aplicativos móveis, use o armazenamento persistente do dispositivo para persistir a ID.<br /><br/>Na próxima vez que o usuário usar o aplicativo no dispositivo, obtenha a ID do cliente persistente.</li></ul><br /> **OBSERVAÇÃO:** as respostas do Bing podem ou não incluir esse cabeçalho. Se a resposta incluir esse cabeçalho, capture a ID do cliente e use-a para todas as solicitações seguintes do Bing para o usuário no dispositivo.<br /><br /> **OBSERVAÇÃO:** se você incluir o X-MSEdge-ClientID, não deverá incluir cookies na solicitação.|  
|<a name="clientip" />X-MSEdge-ClientIP|Cabeçalho de solicitação opcional.<br /><br /> O endereço IPv4 ou IPv6 do dispositivo cliente. O endereço IP é usado para descobrir o local do usuário. O Bing usa as informações de local para determinar o comportamento da pesquisa segura.<br /><br /> **OBSERVAÇÃO:** embora isso seja opcional, você é incentivado a sempre especificar esse cabeçalho e o cabeçalho X-Search-Location.<br /><br /> Não oculte o endereço (por exemplo, alterando o último octeto para 0). Se você ocultar o endereço, isso fará com que o local não fique em nenhum lugar próximo ao local real do dispositivo, o que poderá resultar no fornecimento de resultados incorretos pelo Bing.|  
|<a name="location" />X-Search-Location|Cabeçalho de solicitação opcional.<br /><br /> Uma lista delimitada por ponto-e-vírgula de pares chave/valor que descrevem a localização geográfica do cliente. O Bing usa as informações de local para determinar o comportamento da pesquisa segura e retornar o conteúdo local relevante. Especifique o par chave/valor como \<key\>:\<value\>. Veja a seguir as chaves usadas para especificar o local do usuário.<br /><br /><ul><li>lat – obrigatório. A latitude do local do cliente, em graus. A latitude precisa ser superior ou igual a -90,0 e inferior ou igual a +90,0. Valores negativos indicam latitudes sul e valores positivos indicam latitudes norte.<br /><br /></li><li>long – obrigatório. A longitude do local do cliente, em graus. A longitude precisa ser superior ou igual a -180,0 e inferior ou igual a +180,0. Valores negativos indicam longitudes oeste e valores positivos indicam longitudes leste.<br /><br /></li><li>re – obrigatório. O raio, em metros, que especifica a precisão horizontal das coordenadas. Passe o valor retornado pelo serviço de local do dispositivo. Os valores típicos podem ser 22 m para GPS/Wi-Fi, 380 m para triangulação de torres de celular e 18.000 m para a pesquisa inversa de IP.<br /><br /></li><li>ts – opcional. O carimbo de data/hora UTC UNIX do período em que o cliente estava no local. (O carimbo de data/hora UNIX é o número de segundos desde 1º de janeiro de 1970.)<br /><br /></li><li>head – opcional. A direção ou o curso da viagem relativo do cliente. Especifique a direção da viagem como graus de 0 a 360, no sentido horário em relação ao norte verdadeiro. Especifique essa chave somente se a chave `sp` for diferente de zero.<br /><br /></li><li>sp – opcional. A velocidade horizontal, em metros por segundo, na qual o dispositivo cliente está viajando.<br /><br /></li><li>alt – opcional. A altitude do dispositivo cliente, em metros.<br /><br /></li><li>are – opcional. O raio, em metros, que especifica a precisão vertical das coordenadas. Especifique essa chave somente se você especificar a chave `alt`.<br /><br /></li></ul> **OBSERVAÇÃO:** embora muitas das chaves sejam opcionais, quanto mais informações você fornecer, mais precisos serão os resultados de local.<br /><br /> **OBSERVAÇÃO:** embora isso seja opcional, você é incentivado a sempre especificar a localização geográfica do usuário. O fornecimento do local é especialmente importante se o endereço IP do cliente não reflete precisamente o local físico do usuário (por exemplo, se o cliente usa VPN). Para obter melhores resultados, você deve incluir esse cabeçalho e o cabeçalho X-MSEdge-ClientIP, mas, no mínimo, você deve incluir esse cabeçalho.|

> [!NOTE] 
> Lembre-se de que os Termos de Uso exigem conformidade com todas as leis aplicáveis, incluindo o uso desses cabeçalhos. Por exemplo, em algumas jurisdições, como na Europa, há requisitos para obter o consentimento do usuário antes de colocar determinados dispositivos de monitoramento em dispositivos do usuário.


<a name="content-form-types" />

### <a name="content-form-types"></a>Tipos de formulário de conteúdo

Todas as solicitações precisam incluir o cabeçalho Content-Type. O cabeçalho precisa ser definido como multipart/form-data; boundary=\<boundary string\>, em que \<boundary string\> é uma cadeia de caracteres exclusiva e opaca que identifica o limite dos dados de formulário. Por exemplo, boundary=boundary_1234-abcd.


Se você enviar uma URL ou um token de imagem à Pesquisa Visual, inclua os dados de formulário mostrados a seguir no corpo do POST. Os dados de formulário devem incluir o cabeçalho Conteúdo-Disposição e o parâmetro `name` deve ser definido como "knowledgeRequest." Para obter detalhes sobre o objeto `imageInfo`, consulte [A solicitação](#the-request).


```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "url" : "https://contoso.com/2018/05/fashion/red.jpg"
    }
}

--boundary_1234-abcd--
```

Se você fizer upload de uma imagem local, inclua no corpo do POST os dados de formulário mostrados a seguir. Os dados de formulário precisam incluir o cabeçalho Content-Disposition. Seu parâmetro `name` precisa ser definido como "image" e o parâmetro `filename` pode ser definido como qualquer cadeia de caracteres. O cabeçalho Content-Type pode ser definido como qualquer tipo MIME de imagem geralmente usado. O conteúdo do formulário é o binário da imagem. O tamanho máximo de imagem que você pode fazer upload é 1 MB. A maior da largura ou altura deve ser de 1.500 pixels ou menos.


```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
Content-Type: image/jpeg

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

O exemplo a seguir mostra como especificar a região de interesse de uma imagem de upload.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "cropArea" : {
            "top" : 0.2,
            "left" : 0.3,
            "bottom" : 0.7,
            "right" : 0.6
        }
    }
}

--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="image"
Content-Type: image/jpeg


ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```



### <a name="example-request"></a>Solicitação de exemplo

O exemplo a seguir mostra uma solicitação de insights de imagem completa que passa um token de imagem e a região de interesse. Você recebe o token de insights de uma chamada anterior a /images/search.


```  
POST https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch?mkt=en-us HTTP/1.1  
Content-Type: multipart/form-data; boundary=boundary_1234-abcd
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com 

--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "imageInsightsToken" : "mid_D6426898706EC7..."
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.2,
            "bottom" : 0.7,
            "right" : 0.5
        }
    }
}

--boundary_1234-abcd--
```


## <a name="the-response"></a>A resposta

Se houver insights disponíveis para a imagem, a resposta conterá uma ou mais `tags` que contêm os insights. O campo `image` contém o token de insights para a imagem de entrada.

```json
{
  "_type" : "ImageKnowledge",
  "tags" : [
    {...},
    {...},
    {...},
    {...},
    {...}
  ],
  "image" : {
    "imageInsightsToken" : "bcid_AF8C9CA409421B..."
  }
}
```

O campo `tags` contém um nome de exibição e uma lista de ações (insights). Uma das marcas contém um campo `displayName` que é definido como uma cadeia de caracteres vazia. Essa marcação contém os insights padrão, como páginas da Web que incluem a imagem, imagens visualmente semelhantes e fontes de compra para itens encontrados na imagem. Como a imagem inteira é de interesse, a marcação de insights padrão não inclui caixas delimitadoras para as áreas de interesse.


```json
{
  "_type" : "ImageKnowledge",
  "tags" : [
    {
      "displayName" : "",
      "actions" : [
        {...},
        {...},
        {...},
        {...}
      ]
    },
    {...},
    {...},
    {...},
    {...}
  ],
  "image" : {
    "imageInsightsToken" : "bcid_AF8C9CA409421B..."
  }
}
```

Para obter uma lista dos insights padrão, confira [Insights padrão](./default-insights-tag.md).



As marcas restantes contêm outros insights que podem ser de interesse para o usuário. Por exemplo, se a imagem contiver um texto, uma das marcas poderá incluir um insight de TextResults, que contém o texto reconhecido. Ou se o Bing reconhecer uma entidade (pessoa, lugar ou coisa) na imagem, uma das marcas poderá identificar a entidade. A Pesquisa Visual também retorna um conjunto diversificado de termos (marcas) derivados da imagem de entrada. Essas marcas permitem aos usuários explorar os conceitos encontrados na imagem. Por exemplo, se a imagem de entrada for a de um atleta famoso, uma das marcas poderá ser Esportes, que contém links para imagens de esportes.

Cada marcação inclui um nome de exibição que você pode usar para categorizar o insight, uma caixa delimitadora que identifica a região de interesse à qual o insight se aplica, os próprios insights e uma miniatura da imagem. Por exemplo, se a imagem for de uma pessoa usando uma camisa de uniforme esportivo, uma das marcas poderá incluir uma caixa delimitadora que delimita a camisa e inclui insights de VisualSearch e ProductVisualSearch. Outra marcação pode incluir um insight de ImageResults que contém uma URL para uma solicitação de API /images/search para obter imagens relacionadas a um tópico ou a uma URL de pesquisa do Bing.com que leva o usuário aos resultados da pesquisa de imagem do Bing.com.

Todas as marcações que não sejam a marcação de insights padrão incluem caixas delimitadoras que identificam áreas de interesse na imagem. Por exemplo, se a imagem incluir várias pessoas reconhecidas, as marcas poderão incluir caixas delimitadoras para cada uma das pessoas ou se a imagem contiver itens de vestuário reconhecidos, as marcas poderão incluir caixas delimitadoras para cada item de vestuário reconhecido. Você pode usar a caixa delimitadora para criar pontos de acesso sobre a imagem quando ela receber um clique e fornecer detalhes sobre o conteúdo nessa região da imagem. Você não deve incluir pontos de acesso em uma imagem para caixas delimitadoras que identificam a imagem inteira.

### <a name="text-recognition"></a>Reconhecimento de texto

Se a imagem contiver um texto reconhecido pelo serviço, uma das marcas conterá um insight de TextResults (ação). O `displayName` do insight contém o texto reconhecido. 

```json
    {
        "image" : {
            "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23Text..."
        },
        "displayName" : "##TextRecognition",
        "boundingBox" : {
            "queryRectangle" : {
                "topLeft" : {"x" : 0, "y" : 0},
                "topRight" : {"x" : 1, "y" : 0},
                "bottomRight" : {"x" : 1, "y" : 1},
                "bottomLeft" : {"x" : 0, "y" : 1}
            },
            "displayRectangle" : {
                "topLeft" : {"x" : 0, "y" : 0},
                "topRight" : {"x" : 1, "y" : 0},
                "bottomRight" : {"x" : 1, "y" : 1},
                "bottomLeft" : {"x" : 0, "y" : 1}
            }
        },
        "actions" : [{
            "displayName" : "WALK BIKE ACROSS BRIDGE",
            "actionType" : "TextResults"
        }],
        "sources" : ["OCR"]
    }
```

Como o campo `displayName` da marcação contém ##TextRecognition, não utilize-o como um título de categoria na experiência do usuário. Isso serve para qualquer nome de exibição que comece com ##. Em vez disso, use o nome de exibição da ação.


O reconhecimento de texto também pode reconhecer as informações de contato em cartões de visita, como números de telefone e endereços de email. A caixa delimitadora identifica o local das informações de contato no cartão. 

```json
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.635, "y" : 0},
          "topRight" : {"x" : 0.77, "y" : 0},
          "bottomRight" : {"x" : 0.77, "y" : 0.4873333},
          "bottomLeft" : {"x" : 0.635, "y" : 0.4873333}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.635, "y" : 0},
          "topRight" : {"x" : 0.77, "y" : 0},
          "bottomRight" : {"x" : 0.77, "y" : 0.4873333},
          "bottomLeft" : {"x" : 0.635, "y" : 0.4873333}
        }
      },
      "actions" : [
        {
          "url" : "tel:888%20555%201212",
          "actionType" : "Uri"
        }
      ],
      "sources" : ["OCR"]
    },
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.63, "y" : 0},
          "topRight" : {"x" : 0.866, "y" : 0},
          "bottomRight" : {"x" : 0.866, "y" : 0.5553334},
          "bottomLeft" : {"x" : 0.63, "y" : 0.5553334}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.63, "y" : 0},
          "topRight" : {"x" : 0.866, "y" : 0},
          "bottomRight" : {"x" : 0.866, "y" : 0.5553334},
          "bottomLeft" : {"x" : 0.63, "y" : 0.5553334}
        }
      },
      "actions" : [
        {
          "url" : "mailto:someone@outlook.com",
          "actionType" : "Uri"
        }
      ],
      "sources" : ["OCR"]
    },
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0, "y" : 0},
          "topRight" : {"x" : 1, "y" : 0},
          "bottomRight" : {"x" : 1, "y" : 1},
          "bottomLeft" : {"x" : 0, "y" : 1}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0, "y" : 0},
          "topRight" : {"x" : 1, "y" : 0},
          "bottomRight" : {"x" : 1, "y" : 1},
          "bottomLeft" : {"x" : 0, "y" : 1}
        }
      },
      "actions" : [
        {
          "displayName" : "CHARLENE WHITNEY Graphic Designer 888 555 1212 someone@outlook.com www.contoso.com",
          "actionType" : "TextResults"
        }
      ],
      "sources" : ["OCR"]
    }
```

Se a imagem contiver uma entidade reconhecida como uma pessoa, um lugar ou uma coisa, uma das marcas poderá incluir um insight de Entity. 

```json
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Statue+of+Liberty..."
      },
      "displayName" : "Statue of Liberty",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.40625, "y" : 0.1757813},
          "topRight" : {"x" : 0.6171875, "y" : 0.1757813},
          "bottomRight" : {"x" : 0.6171875, "y" : 0.3867188},
          "bottomLeft" : {"x" : 0.40625, "y" : 0.3867188}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.40625, "y" : 0.1757813},
          "topRight" : {"x" : 0.6171875, "y" : 0.1757813},
          "bottomRight" : {"x" : 0.6171875, "y" : 0.3867188},
          "bottomLeft" : {"x" : 0.40625, "y" : 0.3867188}
        }
      },
      "actions" : [
        {
          "_type" : "ImageEntityAction",
          "webSearchUrl" : "https:\/\/www.bing.com\/search?q=Statue+of+Liberty",
          "displayName" : "Statue of Liberty",
          "actionType" : "Entity",
        }
      ]
    }
```



## <a name="next-steps"></a>Próximas etapas

Para se familiarizar rapidamente com sua primeira solicitação, confira os inícios rápidos: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md).

Experimente a API. Acesse o [Console de Teste da API de Pesquisa Visual](https://dev.cognitive.microsoft.com/docs/services/878c38e705b84442845e22c7bff8c9ac). 


Familiarize-se com a [Referência da API de Pesquisa Visual](https://aka.ms/bingvisualsearchreferencedoc). A referência contém a lista de pontos de extremidade, cabeçalhos e parâmetros de consulta que você usaria para solicitar os resultados da pesquisa. Ela também inclui as definições dos objetos de resposta. 

Leia os [Requisitos de exibição e uso do Bing](./use-and-display-requirements.md) para não violar nenhuma das regras sobre como usar os resultados da pesquisa.


