---
title: Tutorial sobre indexação, consulta e filtragem no Azure Search usando o portal | Microsoft Docs
description: Neste tutorial, use o portal do Azure e dados de exemplo predefinidos para gerar um índice no Azure Search. Explore a pesquisa de texto completo, filtros, facetas, pesquisa difusa, pesquisa geográfica e muito mais.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.topic: tutorial
ms.date: 07/10/2018
ms.author: heidist
ms.openlocfilehash: 0eb6701a7ea08c2dd63bd8b5d7d7c805e6eb1376
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365078"
---
# <a name="tutorial-use-built-in-tools-for-azure-search-indexing-and-queries"></a>Tutorial: Usar ferramentas internas para indexação e consultas do Azure Search

Em uma página do serviço Azure Search no portal do Azure, você pode usar ferramentas internas para fazer testes de conceito e ter experiência prática com o mínimo de aprendizado. As ferramentas do portal não são totalmente iguais às das APIs .NET e REST, mas para um teste rápido de prova de conceito, os assistentes e editores auxiliam com facilidade. Esta introdução sem código apresenta um conjunto de dados pequeno publicado para que você possa codificar consultas interessantes imediatamente. 

> [!div class="checklist"]
> * Comece com os dados de exemplo públicos e gere automaticamente um índice do Azure Search usando o assistente **Importar dados**. 
> * Exibir esquema de índice e atributos de qualquer índice publicado no Azure Search.
> * Explore a pesquisa de texto completo, filtros, facetas, pesquisa difusa e pesquisa geográfica com o **Search Explorer**.  

As ferramentas do portal não dão suporte aos recursos completos do Azure Search. Se as ferramentas forem muito limitadas, considere usar a [Introdução à programação do Azure Search no .NET baseada em código](search-howto-dotnet-sdk.md) ou [Ferramentas de teste da Web para fazer chamadas à API REST](search-fiddler.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. Você também pode assistir a uma demonstração de seis minutos das etapas deste tutorial a partir do minuto 3 neste [vídeo de visão geral do Azure Search](https://channel9.msdn.com/Events/Connect/2016/138).

## <a name="prerequisites"></a>Pré-requisitos

[Crie um serviço Azure Search](search-create-service-portal.md) ou encontre um serviço existente em sua assinatura atual. 

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Abra o painel de serviços de seu serviço de Azure Search. Se você não fixar o bloco do serviço ao seu painel, poderá encontrar o serviço desta maneira: 
   
   * Na barra de navegação, clique em **Todos os serviços** no painel de navegação à esquerda.
   * Na caixa de pesquisa, digite *search* para obter uma lista de serviços de pesquisa da sua assinatura. Clique em **Serviços Search**. Seu serviço deve aparecer na lista. 

### <a name="check-for-space"></a>Verificar o espaço
Muitos clientes começam com o serviço gratuito. Essa versão é limitada a três índices, três fontes de dados e três indexadores. Verifique se há espaço para itens extras antes de começar. Este tutorial cria uma unidade de cada objeto. 

> [!TIP] 
> Os blocos no painel de serviço mostram quantos índices, indexadores e fontes de dados você já tem. O bloco Indexador mostra indicadores de sucesso e falha. Clique no bloco para exibir a contagem do indexador. 
>
> ![Blocos de indexadores e fontes de dados][1]
>

## <a name="create-index"></a> Criar um índice e carregar dados
Consultas de pesquisa são iteradas em um [*índice*](search-what-is-an-index.md) que contém os dados pesquisáveis, os metadados e as construções usados para otimizar certos comportamentos de pesquisa.

Para mantermos essa tarefa baseada no portal, utilizamos um conjunto de dados interno de exemplo que pode ser rastreado com um [*indexador*](search-indexer-overview.md) via assistente **Importar dados**. Um indexador é um rastreador específico de fonte que pode ler metadados e conteúdo de fontes de dados do Azure que tenham suporte. No código, você pode criar e gerenciar indexadores como recursos independentes. No portal, os indexadores são expostos por meio do assistente **Importar dados**. 

#### <a name="step-1-start-the-import-data-wizard"></a>Etapa 1: Iniciar o assistente Importar dados
1. No painel de serviço de Azure Search, clique em **Importar dados** na barra de comandos para iniciar um assistente que cria e preenche um índice.
   
    ![Comando Importar de dados][2]

2. No assistente, clique em **Conectar-se aos seus dados** > **Exemplos** > **realestate-us-sample**. Essa fonte de dados é pré-configurada com um nome, um tipo e as informações de conexão. Depois de criada, ela se torna uma "fonte de dados existente" que pode ser reutilizada em outras operações de importação.

    ![Selecionar o conjunto de dados de exemplo][9]

3. Clique em **OK** para usá-lo.

#### <a name="skip-cognitive-skills"></a>Ignorar Habilidades cognitivas

**Importar dados** inclui uma etapa opcional de habilidades cognitivas que adiciona algoritmos de inteligência artificial para indexação. Esse recurso não é abordado neste tutorial e, portanto, você deve pular para **Personalizar índice de destino**. Se você estiver curioso sobre o novo recurso de versão prévia de pesquisa cognitiva no Azure Search, experimente o [início rápido de pesquisa cognitiva](cognitive-search-quickstart-blob.md) ou o [tutorial](cognitive-search-tutorial-blob.md).

   ![Ignorar etapa de habilidades cognitivas][11]

#### <a name="step-2-define-the-index"></a>Etapa 2: definir o índice
A criação de um índice é geralmente manual e baseada em código, mas o assistente pode gerar um índice para qualquer fonte de dados que ele possa rastrear. No mínimo, um índice requer um nome e uma coleção de campos, com um campo marcado como a chave do documento para identificar cada documento de forma exclusiva.

Campos têm atributos e tipos de dados. As caixas de seleção na parte superior são *atributos de índice* que controlam como o campo é usado. 

* **Recuperável** significa que ele aparece na lista de resultados da pesquisa. Você pode marcar campos individuais como fora dos limites para os resultados de pesquisa ao desmarcar essa caixa de seleção, por exemplo, quando os campos forem usados somente em expressões de filtro. 
* **Filtrável**, **Classificável** e **Com faceta** determinam se um campo pode ser usado em um filtro, em uma classificação ou em uma estrutura de navegação com facetas. 
* **Pesquisável** significa que um campo é incluído na pesquisa de texto completo. As cadeias de caracteres são pesquisáveis. Campos numéricos e boolianos geralmente são marcados como não pesquisáveis. 

Por padrão, o assistente procura na fonte de dados identificadores exclusivos como base para o campo de chave. As cadeias de caracteres são atribuídas como recuperáveis e pesquisáveis. Os números inteiros são atribuídos como recuperáveis, pesquisáveis, classificáveis e facetáveis.

  ![Índice realestate gerado][3]

Clique em **OK** para criar o índice.

#### <a name="step-3-define-the-indexer"></a>Etapa 3: definir o indexador
Ainda no assistente **Importar dados**, clique em **Indexador** > **Nome**e digite um nome para o indexador. 

Esse objeto define um processo executável. Você poderá colocá-lo na agenda de recorrência, mas, por enquanto, use a opção padrão para executar o indexador imediatamente logo que clicar em **OK**.  

  ![indexador realestate][8]

## <a name="check-progress"></a>Verificar o andamento
Para monitorar a importação dos dados, volte para o painel do serviço, role para baixo e clique duas vezes no bloco **Indexadores** para abrir a lista de indexadores. Você deve ver o indexador recém-criado na lista, com status indicando "em andamento" ou sucesso, juntamente com o número de documentos indexados.

   ![Mensagem de andamento do indexador][4]

## <a name="view-the-index"></a>Exibir índice

Os blocos no painel de serviço fornecem informações de resumo e acesso a informações detalhadas. Por exemplo, no bloco **Índices** lado a lado, você deve ver uma lista dos índices já criados, incluindo o índice *realestate-us-sample* que você criou na etapa anterior.

Clique no índice *realestate-us-sample* agora para exibir as opções do portal para sua definição. Uma opção **Adicionar/Editar Campos** permite que você crie e atribua novos campos totalmente. Os campos existentes têm uma representação física no Azure Search e, portanto, não podem ser modificados, nem mesmo no código. Para alterar fundamentalmente um campo existente, crie um novo e o descarte original. 

   ![exemplo de definição de índice][10]

Outros constructos, como perfis de pontuação e opções de CORS, podem ser adicionados a qualquer momento. 

Para entender claramente o que é possível editar ou não durante o design de índice, reserve um minuto para ver as opções de definição de índice. Opções esmaecidas são um indicador de que um valor não pode ser modificado ou excluído.

## <a name="query-index"></a> Consultar o índice
Agora você terá um índice de pesquisa que está pronto para consulta na página de consulta interna [**Gerenciador de pesquisa** ](search-explorer.md). Ele fornece uma caixa de pesquisa para que você possa testar cadeias de caracteres de consulta arbitrárias. 

> [!TIP]
> No [vídeo Visão geral do Azure Search](https://channel9.msdn.com/Events/Connect/2016/138), as etapas a seguir são demonstradas em 6m08s no vídeo.
>

1. Clique em **Gerenciador de pesquisa** na barra de comandos.

   ![Comando Search Explorer][5]

2. Clique em **Alterar índice** na barra de comando para alternar para *realestate-us-sample*. Clique em **Definir versão de API** na barra de comandos para ver quais APIs REST estão disponíveis. Para as consultas abaixo, use a versão disponível (2017-11-11). 

   ![Comandos de índice e API][6]

3. Na barra de pesquisa, insira as cadeias de caracteres de consulta abaixo e clique em **Pesquisar**.

    > [!NOTE]
    > O **Search Explorer** só é capaz de manipular [solicitações da API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents). Ele aceita a sintaxe para a [sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) e para o [analisador de consulta Lucene completo](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), além de todos os parâmetros de pesquisa disponíveis nas operações [Pesquisar Documento](https://docs.microsoft.com/rest/api/searchservice/search-documents).
    > 


#### <a name="example-string-searchseattle"></a>Exemplo (cadeia de caracteres): `search=seattle`

+ O parâmetro **search** é usado para inserir uma palavra-chave de pesquisa de texto completo, neste caso, retornando listagens no estado de King County, estado de Washington, contendo *Seattle* em algum campo pesquisável no documento. 

+ O **Search Explorer** retorna os resultados em JSON, que é detalhado e difícil de ler quando os documentos têm uma estrutura densa. Isso é intencional; a visibilidade de todo o documento é importante o desenvolvimento, especialmente durante o teste. Para uma melhor experiência de usuário, você precisará escrever código que [trate os resultados](search-pagination-page-layout.md) para destacar elementos importantes.

+ Os documentos contêm todos os campos marcados como recuperáveis no índice. Para exibir os atributos de índice no portal, clique em *realestate-us-sample* no bloco **Índices**.

#### <a name="example-parameterized-searchseattlecounttruetop100"></a>Exemplo (com parâmetros): `search=seattle&$count=true&$top=100`

+ O símbolo **&** é usado para acrescentar os parâmetros de pesquisa, que podem ser especificados em qualquer ordem. 

+  O parâmetro **$count=true** retorna uma contagem da soma de todos os documentos retornados. Esse valor aparece próximo ao início dos resultados da pesquisa. Você pode verificar consultas de filtro monitorando alterações relatadas por **$count=true**. Contagens menores indicam que seu filtro está funcionando.

+ O **$top=100** retorna o 100 documentos com maior classificação do total. Por padrão, o Azure Search retorna as primeiras 50 melhores correspondências. Você pode aumentar ou diminuir a quantidade via **$top**.

## <a name="filter-query"></a> Filtrar a consulta

Os filtros são incluídos nas solicitações de pesquisa quando você acrescenta o parâmetro **$filter**. 

#### <a name="example-filtered-searchseattlefilterbeds-gt-3"></a>Exemplo (filtrado): `search=seattle&$filter=beds gt 3`

+ O parâmetro **$filter** retorna resultados que correspondem aos critérios fornecidos. Nesse caso, quartos maiores que 3. 

+ A sintaxe de filtro é uma construção de OData. Para saber mais, confira [Sintaxe de filtro OData](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

## <a name="facet-query"></a> Facetar a consulta

Os filtros de faceta estão incluídos em solicitações de pesquisa. Você pode usar o parâmetro facet para retornar uma contagem agregada de documentos que correspondam a um valor de faceta que você fornecer. 

#### <a name="example-faceted-with-scope-reduction-searchfacetcitytop2"></a>Exemplo (facetado com redução de escopo): `search=*&facet=city&$top=2`

+ **search=*** é uma pesquisa vazia. Pesquisas vazias pesquisam tudo. Um motivo de envio de uma consulta vazia é fazer a filtragem ou faceta no conjunto completo de documentos. Por exemplo, você deseja que uma estrutura de navegação de facetas contenha todas as cidades no índice.

+  **facet** retorna uma estrutura de navegação que você pode passar para um controle de interface do usuário. Ela retorna categorias e uma contagem. Nesse caso, categorias se baseiam na quantidade de cidades. Não há nenhuma agregação no Azure Search, mas você pode aproximar agregação com `facet`, que retorna uma contagem de documentos em cada categoria.

+ **$top=2** recupera dois documentos, ilustrando a que você pode usar `top` para reduzir ou aumentar os resultados.

#### <a name="example-facet-on-numeric-values-searchseattlefacetbeds"></a>Exemplo (faceta em valores numéricos): `search=seattle&facet=beds`**

+ Essa consulta é faceta para camas em uma pesquisa de texto para *Seattle*. O termo *beds* pode ser especificado como uma faceta porque o campo é marcado como recuperável, filtrável e facetável no índice e valores que ele contém (numérico, 1 a 5) são adequados para categorizar as listagens em grupos (listagens com 3 quartos, 4 quartos). 

+ Somente campos filtráveis podem ser facetados. Somente os campos recuperáveis podem ser retornados nos resultados.

## <a name="highlight-query"></a> Adicionar realce

Realce de ocorrências refere-se à formatação de texto correspondentes à palavra-chave, considerando que existam correspondências em um campo específico. Se o termo de pesquisa estiver escondido em uma descrição, você poderá adicionar o realce de ocorrências para facilitar a localização. 

#### <a name="example-highlighter-searchgranite-countertopshighlightdescription"></a>Exemplo (marca-texto): `search=granite countertops&highlight=description`

+ Neste exemplo, a frase formatada *balcões de granito* é mais fácil de identificar no campo de descrição.

#### <a name="example-linguistic-analysis-searchmicehighlightdescription"></a>Exemplo (análise linguística): `search=mice&highlight=description`

+ A pesquisa de texto completo localiza formas com semântica semelhante. Nesse caso, os resultados da pesquisa contêm texto realçado para "rato", para residências que têm a infestação de ratos, em resposta a uma pesquisa de palavra-chave "ratos". Formas diferentes da mesma palavra podem aparecer nos resultados devido a análise linguística. 

+ O Azure Search dá suporte a 56 analisadores da Lucene e da Microsoft. O padrão usado pelo Azure Search é o analisador Lucene standard. 

## <a name="fuzzy-search"></a> Experimentar pesquisa difusa

Por padrão, as palavras com grafia incorreta, como *samamish* para o planalto Sammamish na área de Seattle, não retornam correspondências em uma pesquisa típica. O exemplo a seguir não retorna resultados.

#### <a name="example-misspelled-term-unhandled-searchsamamish"></a>Exemplo (termo digitado incorretamente, sem tratamento): `search=samamish`

Para lidar com erros de ortografia, você pode usar a pesquisa difusa. A pesquisa difusa é habilitada quando você usa a sintaxe de consulta Lucene completa, que ocorre quando você faz duas coisas: definir **queryType = full** na consulta e acrescentar o **~** na cadeia de caracteres de pesquisa. 

#### <a name="example-misspelled-term-handled-searchsamamishquerytypefull"></a>Exemplo (termo digitado incorretamente, com tratamento): `search=samamish~&queryType=full`

Agora, o exemplo retorna documentos que incluem a correspondências com "Sammamish".

Quando **queryType** for especificado, o analisador de consulta simples padrão será usado. O analisador de consulta simples é mais rápido, mas se você precisar de pesquisa difusa, expressões regulares, pesquisa por proximidade ou outros tipos de consulta avançada, será necessário obter a sintaxe completa. 

A pesquisa difusa e a pesquisa curinga têm implicações nos resultados da pesquisa. A análise linguística não é executada nesses formatos de consulta. Antes de usar pesquisas difusa e curinga, reveja [Como a pesquisa de texto completa funciona no Azure Search](search-lucene-query-architecture.md#stage-2-lexical-analysis) e procure a seção sobre exceções à análise léxica.

Para saber mais sobre cenários de consulta habilitados pelo analisador de consulta completo, confira [Sintaxe de consulta Lucene no Azure Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search).

## <a name="geo-search"></a> Experimentar a pesquisa geoespacial

A pesquisa geográfica tem suporte pelo [tipo de dados edm.GeographyPoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) em um campo que contém coordenadas. A pesquisa geográfica é um tipo de filtro, especificado na [sintaxe do filtro OData](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-122121513-47673988-le-5"></a>Exemplo (filtros de coordenadas geográficas): `search=*&$count=true&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5`

O exemplo de consulta filtra os dados posicionais de todos os resultados e os resultados ficam a menos de 5 km de um determinado ponto (especificado como coordenadas de latitude e longitude). Ao adicionar **$count**, você poderá ver quantos resultados são retornados quando se altera a distância ou as coordenadas. 

A pesquisa geográfica é útil se seu aplicativo de pesquisa tem um recurso de “encontrar nas proximidades” ou usa a navegação de mapa. Entretanto, ela não é uma pesquisa de texto completo. Se você tiver requisitos de usuário para pesquisar em uma cidade ou país por nome, adicione campos que contêm nomes de cidade ou países, além de coordenadas.

## <a name="takeaways"></a>Observações

Este tutorial demonstra as etapas básicas para usar o assistente **Importar dados** assistente e o **Search Explorer** no portal do Azure.

Como a força motriz por trás do assistente de importação de dados, você aprendeu sobre [indexadores](search-indexer-overview.md) e sobre o fluxo de trabalho básico para criar índices, incluindo [modificações com suporte em um índice publicado](ttps://docs.microsoft.com/rest/api/searchservice/update-index). 

Você conheceu a sintaxe de consulta por meio de exemplos práticos que demonstram os principais recursos, como filtros, realce de ocorrências, pesquisa difusa e pesquisa geográfica.

Por fim, você aprendeu a obter informações clicando em blocos no painel para qualquer índice, indexador ou fonte de dados criado para sua assinatura. Posteriormente, ao trabalhar com seus próprios índices ou os criados por seus colegas, você poderá usar o portal para verificar rapidamente a definição de uma fonte de dados ou a construção de uma coleção de campos, sem a necessidade de pesquisar em código não familiar.

## <a name="clean-up-resources"></a>Limpar recursos

A maneira mais rápida de fazer a limpeza depois de um tutorial é excluindo o grupo de recursos que contém o serviço Azure Search. Você pode excluir o grupo de recursos agora para excluir permanentemente todo o conteúdo. No portal, o nome do grupo de recursos está na página Visão geral do serviço Azure Search.

## <a name="next-steps"></a>Próximas etapas

Para explorar mais usando ferramentas do Azure Search, considere o uso de uma ferramenta de teste REST, como Postman ou Fiddler:

> [!div class="nextstepaction"]
> [Ferramentas de teste da Web para chamar APIs REST do Azure Search](search-fiddler.md)


<!--Image references-->
[1]: ./media/search-get-started-portal/tiles-indexers-datasources2.png
[2]: ./media/search-get-started-portal/import-data-cmd2.png
[3]: ./media/search-get-started-portal/realestateindex2.png
[4]: ./media/search-get-started-portal/indexers-inprogress2.png
[5]: ./media/search-get-started-portal/search-explorer-cmd2.png
[6]: ./media/search-get-started-portal/search-explorer-changeindex-se2.png
[7]: ./media/search-get-started-portal/search-explorer-query2.png
[8]: ./media/search-get-started-portal/realestate-indexer2.png
[9]: ./media/search-get-started-portal/import-datasource-sample2.png
[10]: ./media/search-get-started-portal/sample-index-def.png
[11]: ./media/search-get-started-portal/skip-cog-skill-step.png