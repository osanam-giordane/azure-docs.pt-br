---
title: Visão geral da transmissão ao vivo usando os Serviços de Mídia do Azure | Microsoft Docs
description: Este tópico fornece uma visão geral da transmissão ao vivo usando os Serviços de Mídia do Azure v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/06/2018
ms.author: juliako
ms.openlocfilehash: e9ecf1ba3022ca057fa09bad2413aa19d902ae23
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972172"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Transmissão ao vivo com os Serviços de Mídia do Azure v3

Ao distribuir eventos de transmissão ao vivo com os Serviços de Mídia do Azure, normalmente, os seguintes componentes estão envolvidos:

* Uma câmera é usada para transmitir um evento.
* Um codificador de vídeo ao vivo que converte sinais da câmera (ou outro dispositivo, como laptop) em fluxos enviados para o serviço de transmissão ao vivo dos Serviços de Mídia. Os sinais também podem incluir SCTE-35 de publicidade e Indicações de anúncio. 
* O serviço de transmissão ao vivo dos Serviços de Mídia permite que você ingira, visualize, empacote, registre, criptografe e transmita o conteúdo para seus clientes ou para uma CDN, a fim de ampliar a distribuição.

Este artigo fornece uma visão geral detalhada e inclui diagramas dos principais componentes envolvidos na transmissão ao vivo com os Serviços de Mídia.

## <a name="overview-of-main-components"></a>Visão geral dos componentes principais

Nos Serviços de Mídia, [LiveEvents](https://docs.microsoft.com/rest/api/media/liveevents) são responsáveis pelo processamento do conteúdo de transmissão ao vivo. Um LiveEvent fornece um ponto de extremidade de entrada (URL de entrada) que você fornece a um codificador dinâmico local. O LiveEvent recebe fluxos de entrada ao vivo do codificador dinâmico no formato RTMP ou Smooth Streaming e o disponibiliza para streaming por meio de um ou mais [StreamingEndpoints](https://docs.microsoft.com/rest/api/media/streamingendpoints). Um [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) permite que você controle a publicação, gravação e as configurações da janela de DVR do live stream. O LiveEvent também fornece um ponto de extremidade de versão prévia (URL de versão prévia) usado para visualizar e validar o fluxo antes de processamento e entrega adicionais. 

Os Serviços de Mídia fornecem um **Empacotamento Dinâmico** que permite a você visualizar e transmitir seu conteúdo nos formatos de streaming MPEG DASH, HLS, Smooth Streaming, sem a necessidade de empacotar novamente nesses formatos de streaming. Você pode reproduzir com players compatíveis com HLS, DASH ou Smooth. Também é possível usar o [Player de Mídia do Azure](http://amp.azure.net/libs/amp/latest/docs/index.html) para testar o stream.

Com os Serviços de Mídia, você pode entregar seu conteúdo criptografado dinamicamente (**Criptografia Dinâmica**) com a criptografia AES-128 (Advanced Encryption Standard) ou qualquer um dos três principais sistemas de DRM (Gerenciamento de Direitos Digitais): Microsoft PlayReady, Google Widevine e Apple FairPlay. Os serviços de mídia também fornecem um serviço de distribuição de chaves AES e licenças DRM (PlayReady, Widevine e FairPlay) para os clientes autorizados.

Se quiser, também é possível aplicar uma **Filtragem Dinâmica**, que pode ser usada para controlar o número de faixas, formatos, taxas de bits, que são enviadas para os players. Os Serviços de Mídia também dão suporte à inserção de anúncios.


## <a name="liveevent-types"></a>Tipos de LiveEvent

Um [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) pode ser de dois tipos: codificação ativa e de passagem. 

### <a name="live-encoding-with-media-services"></a>Codificação ativa com os Serviços de Mídia

![Codificação ativa](./media/live-streaming/live-encoding.png)

Um codificador ativo local envia uma transmissão de taxa de bits adaptável única para o LiveEvent que está habilitado para executar a codificação ativa com os Serviços de Mídia em um dos seguintes protocolos: RTMP ou Smooth Streaming (MP4 fragmentado). Depois, o LiveEvent realiza a codificação ativa do fluxo de entrada com taxa de bits única em um fluxo de vídeo (adaptável) de múltiplas taxas de bits. Quando solicitado, os Serviços de Mídia transmitem o fluxo aos clientes.

Ao criar esse tipo de LiveEvent, especifique **Básico** (LiveEventEncodingType.Basic).

### <a name="pass-through"></a>Passagem

![passagem](./media/live-streaming/pass-through.png)

A Passagem é otimizada para live streams de longa execução, ou codificação ativa linear 24x7 usando um codificador ativo local. O codificador local envia múltiplas taxas de bits de **RTMP** ou **Smooth Streaming** (MP4 fragmentado) para o LiveEvent que está configurado para a entrega de **passagem**. A entrega de **passagem** ocorre quando as transmissões ingeridas passam pelos **LiveEvents** sem nenhum processamento adicional. 

LiveEvents de passagem são compatíveis com a resolução de 4K, e são passados com HEVC quando usados com o protocolo de ingestão Smooth Streaming. 

Ao criar esse tipo de LiveEvent, especifique **Nenhum** (LiveEventEncodingType.None).

> [!NOTE]
> Usar um método de passagem é a maneira mais econômica de fazer uma transmissão ao vivo quando você estiver fazendo vários eventos durante um longo período e já tiver investido em codificadores locais. Veja os detalhes de [preços](https://azure.microsoft.com/pricing/details/media-services/) .
> 

## <a name="liveevent-types-comparison"></a>Comparação dos tipos de LiveEvent 

A tabela a seguir compara os recursos dos dois tipos de LiveEvent.

| Recurso | LiveEvent de passagem | LiveEvent básico |
| --- | --- | --- |
| A entrada de taxa de bits única é codificada em várias taxas de bits na nuvem |Não  |sim |
| Resolução máxima, número de camadas |4Kp30  |720p, 6 camadas, 30 fps |
| Protocolos de entrada |RTMP, Smooth Streaming |RTMP, Smooth Streaming |
| Preço |Confira a [página de preços](https://azure.microsoft.com/pricing/details/media-services/) e clique na guia “Vídeo ao vivo” |Confira a [página de preços](https://azure.microsoft.com/pricing/details/media-services/) |
| Tempo de execução máximo |24x7 |24x7 |
| Suporte para inserção de imagens fixas |Não  |sim |
| Suporte para sinalização de anúncios via API|Não  |sim |
| Suporte para sinalização de anúncios via SCTE35 inband|sim |sim |
| Legendas CEA 608/708 de passagem |sim |sim |
| Capacidade de recuperação de interrupções breves no feed de contribuição |sim |Não (O LiveEvent começará a exibir imagens fixas após um período superior a 6 segundos sem dados de entrada)|
| Suporte para GOPs de entrada não uniforme |sim |Não – a entrada deve ser fixa em GOPs de 2 s |
| Suporte para entrada de taxa de quadros variável |sim |Não – a entrada deve ser uma taxa de quadros fixa.<br/>Pequenas variações são toleradas, por exemplo, durante cenas ricas em movimento. No entanto, o codificador não poderá reduzir para 10 quadros por segundo. |
| Desligamento automático de LiveEvents quando há perda do feed de entrada |Não  |Após 12 horas, se não houver nenhum LiveOutput em execução |

## <a name="liveevent-states"></a>Estados de LiveEvent 

O estado atual de um LiveEvent. Os valores possíveis incluem:

|Estado|DESCRIÇÃO|
|---|---|
|**Interrompido**| Esse é o estado inicial do LiveEvent após sua criação (a menos que o início automático tenha sido especificado). Não há cobrança nesse estado. Nesse estado, as propriedades do LiveEvent podem ser atualizadas, mas streaming não é permitido.|
|**Iniciando**| O LiveEvent está sendo iniciado. Não há cobrança nesse estado. Nenhuma atualização ou streaming é permitido durante esse estado. Se ocorrer um erro, o LiveEvent retornará para o estado Parado.|
|**Executando**| O LiveEvent é capaz de processar live streams. Agora o uso está sendo cobrado. Você deve parar o LiveEvent para evitar outras cobranças.|
|**Parando**| O LiveEvent está sendo interrompido. Não haverá cobrança nesse estado transitório. Nenhuma atualização ou streaming é permitido durante esse estado.|
|**Excluindo**| O LiveEvent está sendo excluído. Não haverá cobrança nesse estado transitório. Nenhuma atualização ou streaming é permitido durante esse estado.|

## <a name="liveoutput"></a>LiveOutput

Um [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) permite que você controle a publicação, gravação e as configurações da janela de DVR do live stream. A relação entre LiveEvent e LiveOutput é semelhante à mídia tradicional, em que um canal (LiveEvent) tem um fluxo constante de conteúdo, e um programa (LiveOutput) tem como escopo um evento cronometrado nesse LiveEvent.
Você pode especificar o número de horas pelo qual deseja manter o conteúdo gravado para o LiveOutput, definindo a propriedade **ArchiveWindowLength**. **ArchiveWindowLength** é um período ISO 8601 de duração da janela de arquivo (Gravador de Vídeo Digital ou DVR). Esse valor pode ser definido entre o mínimo de 5 minutos e o máximo de 25 horas. 

**ArchiveWindowLength** também determina o número máximo de vezes que os clientes podem voltar no tempo a partir da posição dinâmica atual. Os LiveOutputs podem ser executados no período de tempo especificado, mas o conteúdo que ficar para trás no comprimento da janela será continuamente descartado. O valor desta propriedade também determina por quanto tempo os manifestos do cliente podem crescer.

Cada LiveOutput está associado a um [Ativo](https://docs.microsoft.com/rest/api/media/assets) e registra os dados em um contêiner no armazenamento do Azure associado à conta dos Serviços de Mídia. Para publicar o LiveOutput, você deve criar um [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) para o ativo associado. Ter esse localizador permitirá que você crie uma URL de transmissão que você pode fornecer aos seus clientes.

Um LiveEvent dá suporte a até três LiveOutputs em execução simultânea para que você possa criar diversos arquivos no mesmo fluxo de entrada. Isso permite que você publique e arquive diferentes partes de um evento, conforme necessário. Por exemplo, você precisa transmitir um feed linear ao vivo 24x7, mas deseja criar "gravações" dos programas ao longo do dia, a fim de oferecê-las aos clientes que perderam alguma transmissão como conteúdo sob demanda.  Para esse cenário, primeiro crie um LiveOutput primário, com uma janela de arquivo curto de 1 hora ou menos, para que os clientes sintonizem como live stream primário. Você criaria um StreamingLocator para esse LiveOutput e o publicaria em seu aplicativo ou site como o feed "Ao vivo".  Durante a execução do feed, você pode criar programaticamente um segundo LiveOutput simultâneo no início de uma apresentação (ou cinco minutos antes, a fim ter arestas para aparar posteriormente.) Esse segundo LiveOutput pode ser interrompido cinco minutos após o término do programa ou evento e, em seguida, pode criar um novo StreamingLocator para publicar esse programa como um ativo de sob demanda no catálogo do seu aplicativo.  Repita esse processo várias vezes para outros limites ou destaques de um programa que você deseja compartilhar imediatamente como conteúdo sob demanda, enquanto a transmissão do feed "Ao vivo" do primeiro LiveOutput continua no feed linear.  Além disso, você pode aproveitar o suporte do Filtro Dinâmico para aparar o arquivo do LiveOutput introduzido para "sobreposição de segurança" entre programas, e atingir uma precisão maior do início e fim do conteúdo. O conteúdo arquivado também pode ser enviado para [Transformação](https://docs.microsoft.com/rest/api/media/transforms) para codificação ou enquadramento de subclipping preciso para vários formatos de saída que serão usados como sindicalização para outros serviços.

## <a name="streamingendpoint"></a>StreamingEndpoint

Uma vez que o fluxo está fluindo para o LiveEvent, será possível iniciar o evento de streaming criando um Asset, LiveOutput e StreamingLocator. Isso arquivará o fluxo e o disponibilizará aos espectadores por meio do [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints).

Quando sua conta dos Serviços de Mídia é criada, um ponto de extremidade de streaming padrão é adicionado à sua conta em estado Parado. Para iniciar seu conteúdo de streaming e tirar proveito do empacotamento dinâmico e da criptografia dinâmica, o ponto de extremidade de streaming do qual você deseja transmitir o conteúdo deve estar em estado Executando.

## <a name="billing"></a>Cobrança

Um LiveEvent começará a ser cobrado assim que seu estado mudar para "Executando". Para interromper a cobrança do LiveEvent, você deve Interromper o LiveEvent.

> [!NOTE]
> Quando **LiveEventEncodingType** em seu [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) estiver definido como Básico, os Serviços de Mídia desligarão automaticamente para qualquer LiveEvent que ainda esteja em estado de "Execução" 12 horas após o feed de entrada ser perdido, e não exista nenhum LiveOutput em execução. No entanto, você ainda receberá uma cobrança pelo tempo em que o LiveEvent estava no estado "Execução".
>

A tabela a seguir mostra como os estados de LiveEvent são mapeados para o modo de cobrança.

| Estado de LiveEvent | Trata-se de cobrança? |
| --- | --- |
| Iniciando |Nenhum (estado transitório) |
| Executando |SIM |
| Parando |Nenhum (estado transitório) |
| Parado |Não  |

## <a name="next-steps"></a>Próximas etapas

[Tutorial de live streaming](stream-live-tutorial-with-api.md)
