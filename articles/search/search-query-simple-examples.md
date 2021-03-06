---
title: Exemplos de consulta simples para Azure Search | Microsoft Docs
description: Exemplos de consulta simples para pesquisa de texto completo, pesquisa de filtro, pesquisa geográfica, pesquisa facetada e outras cadeias de caracteres de consulta usadas para consultar um índice do Azure Search.
author: HeidiSteen
manager: cgronlun
tags: Simple query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: heidist
ms.openlocfilehash: e4bb72eb8ad6f15b0e5bc14e0e07556e76d0477b
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2018
ms.locfileid: "39368594"
---
# <a name="simple-syntax-query-examples-for-building-queries-in-azure-search"></a>Exemplos de consulta de sintaxe simples para criar consultas no Azure Search

A [sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) invoca o analisador de consulta padrão para executar consultas de pesquisa de texto completo em um índice do Azure Search. O analisador de consultas simples é rápido e lida com cenários comuns no Azure Search, incluindo pesquisa de texto completo, pesquisa filtrada e facetada e pesquisa geográfica. Neste artigo, percorra exemplos demonstrando as operações de consulta disponíveis ao usar a sintaxe simples.

A sintaxe de consulta alternativa é [Lucene completa](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), com suporte para estruturas de consulta mais complexas como pesquisa com curinga e difusa, que pode demorar mais tempo para ser processada. Para obter mais informações e exemplos que demonstram a sintaxe completa, consulte [Exemplos de consulta de sintaxe Lucene](search-query-lucene-examples.md).

## <a name="formulate-requests-in-postman"></a>Formular solicitações em Postman

Os exemplos a seguir utilizam um índice de pesquisa de Trabalhos de Nova Iorque que consiste em trabalhos disponíveis com base em um conjunto de dados fornecido pelo [OpenData da cidade de Nova Iorque](https://nycopendata.socrata.com/). Esses dados não devem ser considerados atuais ou completos. O índice está em um serviço de área restrita fornecido pela Microsoft, o que significa que não é necessária uma assinatura do Azure ou Azure Search para experimentar essas consultas.

O que é necessário é o Postman ou uma ferramenta equivalente para emitir solicitação HTTP em GET. Para obter mais informações, consulte [Testar com clientes REST](search-fiddler.md).

### <a name="set-the-request-header"></a>Definir o cabeçalho de solicitação

1. No cabeçalho de solicitação, defina **Content-Type** para `application/json`.

2. Adicione uma**api-key** e defina-a para essa cadeia de caracteres: `252044BE3886FE4A8E3BAA4F595114BB`. Essa é uma chave de consulta para o serviço de pesquisa de área restrita que hospeda o índice Trabalhos de Nova Iorque.

Após especificar o cabeçalho de solicitação, você poderá reutilizá-lo para todas as consultas neste artigo, trocando apenas a cadeia de caracteres **search=**. 

  ![Cabeçalho da solicitação do Postman](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>Definir a URL da solicitação

A solicitação é um comando GET emparelhado com uma URL que contém a cadeia de caracteres de pesquisa e o ponto de extremidade do Azure Search.

  ![Cabeçalho da solicitação do Postman](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

A composição de URL possui os elementos a seguir:

+ **`https://azs-playground.search.windows.net/`** é um serviço de pesquisa de área restrita mantido pela equipe de desenvolvimento do Azure Search. 
+ **`indexes/nycjobs/`** é o índice Trabalhos de Nova Iorque na coleção de índices desse serviço. O nome do serviço e índice são obrigatórios na solicitação.
+ **`docs`** é a coleção de documentos que contém todo o conteúdo pesquisável. A chave de API de consulta fornecida no cabeçalho da solicitação somente funciona em operações de leitura direcionando a coleção de documentos.
+ **`api-version=2017-11-11`** define a versão da API, que é um parâmetro obrigatório em todas as solicitações.
+ **`search=*`** é a cadeia de caracteres de consulta, que na consulta inicial é nula, retornando os primeiros 50 resultados (por padrão).

## <a name="send-your-first-query"></a>Enviar a primeira consulta

Como uma etapa de verificação, cole a solicitação a seguir no GET e clique em **Enviar**. Os resultados são retornados como documentos JSON detalhados. Você pode copiar e colar essa URL no primeiro exemplo abaixo.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&search=*
  ```

A cadeia de caracteres de consulta, **`search=*`**, é uma pesquisa não especificada equivalente à pesquisa nula ou vazia. Não é especialmente útil, mas é a pesquisa mais simples que você pode fazer.

Opcionalmente, é possível adicionar **`$count=true`** à URL para retornar uma contagem dos documentos que correspondem aos critérios de pesquisa. Em uma cadeia de caracteres de pesquisa vazia, isso é todos os documentos no índice (2802 no caso de Trabalhos de Nova Iorque).

## <a name="how-to-invoke-simple-query-parsing"></a>Como invocar a análise de consulta simples

Para consultas interativas, não é necessário especificar nada: simples é o padrão. No código, se você invocou anteriormente **queryType=full** para a sintaxe de consulta completa, será possível redefinir de volta ao padrão com **queryType=simple**.

## <a name="example-1-field-scoped-query"></a>Exemplo 1: consulta com escopo de campo

A primeira consulta não é específica da sintaxe (a consulta funciona tanto para sintaxe simples como completa), mas conduzimos com este exemplo a introdução de um conceito de consulta de linha de base que produz uma resposta JSON razoavelmente legível. Por uma questão de brevidade, a consulta direciona apenas o campo *business_title* e especifica que somente os títulos da empresa sejam retornados. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&search=*
```

O parâmetro **searchFields** restringe a pesquisa apenas ao campo de título de negócios. O parâmetro **select** determina quais campos estão incluídos no conjunto de resultados.

A resposta para essa consulta deve ser semelhante à captura de tela a seguir.

  ![Resposta de exemplo do Postman](media/search-query-lucene-examples/postman-sample-results.png)

Você deve ter observado que a pontuação de pesquisa também é retornada para cada documento, mesmo que a pontuação de pesquisa não seja especificada. Isso ocorre porque a pontuação de pesquisa é metadados, com o valor indicando a ordem de classificação dos resultados. Pontuações uniformes de 1 ocorrem quando não há classificação, seja porque a pesquisa não foi pesquisa de texto completo ou porque não há critérios a serem aplicados. Para pesquisa nula, não há critérios e as linhas retornadas estão em ordem arbitrária. Na medida em que o critério de pesquisa adquire mais definição, você verá as pontuações da pesquisa evoluírem em valores significativos.

## <a name="example-2-look-up-by-id"></a>Exemplo 2: pesquisar por ID

Esse exemplo é um pouco atípico, mas ao avaliar os comportamentos da pesquisa é recomendável inspecionar todo o conteúdo de um documento para compreender por que ele foi incluído ou excluído dos resultados. Para retornar um documento inteiro, use uma [Operação de pesquisa](https://docs.microsoft.com/rest/api/searchservice/lookup-document) ara passar a ID do documento.

Todos os documentos possuem um identificador exclusivo. Para experimentar a sintaxe de uma consulta, primeiro retorne uma lista de IDs do documento para que seja possível localizar uma a ser utilizada. Para Trabalhos de Nova Iorque, os identificadores são armazenados no campo `id`.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=id&$select=id&search=*
 ```

O próximo exemplo é uma consulta de pesquisa que retorna um documento específico com base em `id` "9E1E3AF9-0660-4E00-AF51-9B654925A2D5", que apareceu primeiro na resposta anterior. A consulta a seguir retorna o documento inteiro, não apenas os campos selecionados. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2017-11-11&$count=true&search=*
 ```

## <a name="example-3-search-precision"></a>Exemplo 3: precisão da pesquisa

Consultas de termo são termos únicos, talvez muitos deles, avaliados independentemente. Consultas de frase são colocadas entre aspas e avaliadas como uma cadeia de caracteres textual. A precisão da correspondência é controlada pelos operadores e pelo searchMode.

Exemplo 1: **`&search=fire`** retorna 150 resultados, em que todas as correspondências contêm a palavra fogo em algum lugar no documento.

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=fire
```

Exemplo 2: **`&search=fire department`** retorna 2002 resultados. As correspondências são retornadas para documentos que contenham fogo ou bombeiros.

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=fire department
```

Exemplo 3: **`&search="fire department"`** retorna 82 resultados. Colocar a cadeia de caracteres entre aspas é uma pesquisa textual em ambos os termos e as correspondências são localizadas nos termos indexados no índice consistindo dos termos combinados. Isso explica por que uma pesquisa como **`search=+fire +department`** não é equivalente. Ambos os termos são obrigatórios, mas são verificados independentemente. 

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search="fire department"
```

## <a name="example-4-booleans-with-searchmode"></a>Exemplo 4: boolianos com searchMode

A sintaxe simples dá suporte a operadores boolianos na forma de caracteres (`+, -, |`). O parâmetro searchMode informa as compensações entre precisão e recall, com `searchMode=any` favorecendo recall (correspondência em qualquer critério qualifica um documento para o conjunto de resultados) e `searchMode=all` favorecendo a precisão (todos os critérios devem ser combinados). O padrão é `searchMode=any`, o que pode ser confuso se você estiver empilhando uma consulta com vários operadores e obtendo resultados mais amplos em vez de mais restritos. Isso é particularmente verdade com NÃO, onde os resultados incluem todos os documentos "não contendo" um termo específico.

Usando o searchMode padrão (qualquer), 2800 documentos são retornados: aqueles que contêm o termo de várias partes "corpo de bombeiros", além de todos os documentos que não possuem o termo "Metrotech Center".

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchMode=any&search="fire department"  -"Metrotech Center"
```
Alterar searchMode para `all` impõe um efeito cumulativo aos critérios e retorna um conjunto de resultados menor - 21 documentos - que consiste em documentos contendo toda a frase "corpo de bombeiros", menos esses trabalhos no endereço do Metrotech Center.

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchMode=all&search="fire department"  -"Metrotech Center"
```


## <a name="example-5-structuring-results"></a>Exemplo 5: estruturando resultados

Vários parâmetros controlam quais campos estão nos resultados da pesquisa, o número de documentos retornados em cada lote e a ordem de classificação. Esse exemplo ressurge alguns dos exemplos anteriores, limitando os resultados a campos específicos usando a instrução **$select** e o critério de pesquisa textual, retornando 82 correspondências 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"
```
Anexado ao exemplo anterior, é possível classificar por título. Esse tipo funciona porque civil_service_title é *classificável* no índice.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title
```

Os resultados de paginação são implementados usando o parâmetro **$top**, nesse caso, retornando os 5 principais documentos:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=0
```

Para obter os próximos 5, ignore o primeiro lote:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=5
```

## <a name="next-steps"></a>Próximas etapas
Experimente especificar consultas no código. Os links a seguir explicam como configurar consultas de pesquisa para .NET e API REST usando a sintaxe simples padrão.

* [Consultar seu índice do Azure Search usando o SDK do .NET](search-query-dotnet.md)
* [Consultar seu índice do Azure Search usando a API REST](search-query-rest-api.md)

Referência de sintaxe adicional, arquitetura de consulta e exemplos podem ser encontrados nos links a seguir:

+ [Exemplos de consulta de sintaxe Lucene para criar consultas avançadas](search-query-lucene-examples.md)
+ [Como funciona a pesquisa de texto completo no Azure Search](search-lucene-query-architecture.md)
+ [Sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Consulta Lucene completa](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
