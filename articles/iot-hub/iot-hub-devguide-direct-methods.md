---
title: Entender os métodos diretos do Hub IoT do Azure | Microsoft Docs
description: Guia de desenvolvedor – use métodos diretos para invocar código em seus dispositivos de um aplicativo de serviço.
author: nberdy
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: nberdy
ms.openlocfilehash: 0b84d7b0e7bbd2021ea4d3e3e804c739be59b48a
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186869"
---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>Entender e chamar métodos diretos do Hub IoT
O Hub IoT oferece a capacidade de invocar métodos diretos em dispositivos a partir da nuvem. Os métodos diretos representam uma interação entre solicitação e resposta com um dispositivo semelhante a uma chamada HTTP, na qual eles são bem-sucedidos ou falham imediatamente (depois que o tempo limite especificado pelo usuário é atingido). Essa abordagem é útil para cenários em que o curso de ação imediata é diferente dependendo se o dispositivo foi capaz de responder.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Cada método de dispositivo tem como alvo um único dispositivo. Os [Trabalhos][lnk-devguide-jobs] são uma maneira de invocar métodos diretos em vários dispositivos e agendar invocação de método para dispositivos desconectados.

Qualquer pessoa com permissões de **conectar serviço** no Hub IoT pode invocar um método em um dispositivo.

Os métodos diretos seguem um padrão de solicitação e resposta e se destinam a comunicações que exigem confirmação imediata de seus resultados. Por exemplo, controle interativo do dispositivo, como ativar um ventilador.

Veja as [diretrizes de comunicação da nuvem para o dispositivo][lnk-c2d-guidance] se está em dúvida entre o uso de propriedades desejadas, métodos diretos ou mensagens da nuvem para o dispositivo.

## <a name="method-lifecycle"></a>Ciclo de vida do método
Os métodos diretos são implementados no dispositivo e podem precisar ou não de entradas no conteúdo do método para instanciar corretamente. Você invoca um método direto por meio de um URI voltado para serviços (`{iot hub}/twins/{device id}/methods/`). Um dispositivo recebe métodos diretos por meio de um tópico MQTT específico do dispositivo (`$iothub/methods/POST/{method name}/`) ou de links do AMQP (propriedades de aplicativo `IoThub-methodname` e `IoThub-status`). 

> [!NOTE]
> Quando você invoca um método direto em um dispositivo, os valores e nomes de propriedade só podem conter caracteres alfanuméricos imprimíveis US-ASCII, exceto pelo seguinte conjunto: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``.
> 
> 

Os métodos diretos são síncronos e obtêm êxito ou falham após o tempo limite (padrão: 30 segundos, configurável para até 3.600 segundos). Os métodos diretos são úteis em cenários interativos em que você deseja que um dispositivo atue somente, e somente se, o dispositivo estiver online e recebendo comandos. Por exemplo, ativar a luz de um telefone. Nesses cenários, você deseja ver uma falha ou êxito imediatamente, para que o serviço de nuvem possa atuar quanto ao resultado o mais rápido possível. O dispositivo pode retornar algum corpo de mensagem como resultado do método, mas não é obrigatório que o método faça isso. Não há nenhuma garantia quanto à ordenação ou semântica de simultaneidade nas chamadas de método.

Os métodos diretos servem somente para HTTPS do lado da nuvem, e MQTT ou AMQP do lado do dispositivo.

A carga das solicitações e respostas do método é um documento JSON de até 128 KB.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Invocar um método direto de um aplicativo back-end
### <a name="method-invocation"></a>Invocação de método
As invocações de método direto em um dispositivo são chamadas HTTPS, que compreendem:

* O *URI de Solicitação* específico para o dispositivo em conjunto com a [versão da API](/rest/api/iothub/service/invokedevicemethod):

    ```http
    https://fully-qualified-iothubname.azure-devices.net/twins/{deviceId}/methods?api-version=2018-06-30
    ```

* A *método* POST
* *Cabeçalhos* que contêm a autorização, ID da solicitação, tipo de conteúdo e codificação de conteúdo
* Um *corpo* JSON transparente no seguinte formato:

    ```json
    {
        "methodName": "reboot",
        "responseTimeoutInSeconds": 200,
        "payload": {
            "input1": "someInput",
            "input2": "anotherInput"
        }
    }
    ```

Tempo limite em segundos. Se o tempo limite não tiver sido definido, o padrão será 30 segundos.

#### <a name="example"></a>Exemplo

Veja abaixo um exemplo de barebone usando `curl`. 

```bash
curl -X POST \
  https://iothubname.azure-devices.net/twins/myfirstdevice/methods?api-version=2018-06-30 \
  -H 'Authorization: SharedAccessSignature sr=iothubname.azure-devices.net&sig=x&se=x&skn=iothubowner' \
  -H 'Content-Type: application/json' \
  -d '{
    "methodName": "reboot",
    "responseTimeoutInSeconds": 200,
    "payload": {
        "input1": "someInput",
        "input2": "anotherInput"
    }
}'
```

### <a name="response"></a>Response
O aplicativo de back-end recebe uma resposta que inclui:

* *Código de status HTTP*, que é usado para erros provenientes do Hub IoT, incluindo um erro 404 para dispositivos que não estão conectados
* *Cabeçalhos* que contêm a ETag, ID da solicitação, tipo de conteúdo e codificação de conteúdo
* Um *corpo* JSON no seguinte formato:

    ```json
    {
        "status" : 201,
        "payload" : {...}
    }
    ```

    `status` e `body` são fornecidos pelo dispositivo e usados para responder com o código de status e/ou descrição do dispositivo.

### <a name="method-invocation-for-iot-edge-modules"></a>Invocação de método para módulos do Azure IoT Edge
A invocação de métodos diretos usando uma ID de módulo é compatível no SDK da versão prévia C# (disponível [aqui](https://www.nuget.org/packages/Microsoft.Azure.Devices/1.16.0-preview-004)).

Para essa finalidade, use o `ServiceClient.InvokeDeviceMethodAsync()` método e passe em `deviceId` e `moduleId` como parâmetros.

## <a name="handle-a-direct-method-on-a-device"></a>Tratar um método direto em um dispositivo
### <a name="mqtt"></a>MQTT
#### <a name="method-invocation"></a>Invocação de método
Os dispositivos recebem solicitações de método direto sobre o tópico MQTT: `$iothub/methods/POST/{method name}/?$rid={request id}`

O corpo que o dispositivo recebe está no seguinte formato:

```json
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

As solicitações de método são QoS 0.

#### <a name="response"></a>Response
O dispositivo envia as respostas para `$iothub/methods/res/{status}/?$rid={request id}`, em que:

* A propriedade `status` é o status de execução fornecido pelo dispositivo da execução do método.
* A propriedade `$rid` é a ID de solicitação de invocação do método recebida do Hub IoT.

O corpo é definido pelo dispositivo e pode ter qualquer status.

### <a name="amqp"></a>AMQP
#### <a name="method-invocation"></a>Invocação de método
O dispositivo recebe solicitações de método direto criando um link de recebimento no endereço `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`

A mensagem do AMQP chega ao link de recebimento que representa a solicitação do método. Ele contém o seguinte:
* A propriedade de ID de correlação, que contém uma ID de solicitação que deve ser passada de volta com a resposta do método correspondente
* Uma propriedade de aplicativo chamada `IoThub-methodname`, que contém o nome do método que está sendo invocado
* O corpo da mensagem do AMQP que contém a carga do método como JSON

#### <a name="response"></a>Response
O dispositivo cria um link de envio para retornar a resposta do método no endereço `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`

A resposta do método é retornada no link de envio e é estruturada da seguinte forma:
* A propriedade de ID de correlação, que contém a ID da solicitação passada na mensagem de solicitação do método
* Uma propriedade de aplicativo chamada `IoThub-status`, que contém o status do método fornecido pelo usuário
* O corpo da mensagem do AMQP que contém a resposta do método como JSON

## <a name="additional-reference-material"></a>Material de referência adicional
Outros tópicos de referência no Guia do desenvolvedor do Hub IoT incluem:

* [Pontos de extremidade do Hub IoT][lnk-endpoints] descreve os vários pontos de extremidade que cada Hub IoT expõe para operações de tempo de execução e de gerenciamento.
* [Limitação e cotas][lnk-quotas] descreve as cotas que se aplicam e o comportamento de limitação esperado ao usar o Hub IoT.
* [SDKs de dispositivo e serviço IoT do Azure][lnk-sdks] lista os vários SDKs de linguagem que você pode usar no desenvolvimento de aplicativos de dispositivo e de serviço que interagem com o Hub IoT.
* [Linguagem de consulta do Hub IoT para dispositivos gêmeos, trabalhos e roteamento de mensagens][lnk-query] descreve a linguagem de consulta do Hub IoT que você pode usar para recuperar informações do Hub IoT sobre dispositivos gêmeos e trabalhos.
* [Suporte ao MQTT do Hub IoT][lnk-devguide-mqtt] fornece mais informações sobre o suporte do Hub IoT para o protocolo MQTT.

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu a usar métodos diretos, pode ser interessante ler o seguinte artigo do Guia do Desenvolvedor do Hub IoT:

* [Agendar trabalhos em vários dispositivos][lnk-devguide-jobs]

Se você quiser experimentar alguns dos conceitos descritos neste artigo, talvez se interesse pelo seguinte tutorial de Hub IoT:

* [Usar métodos diretos][lnk-methods-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-methods-tutorial]: quickstart-control-device-node.md
[lnk-devguide-messages]: iot-hub-devguide-messaging.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
