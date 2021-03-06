---
title: Copiar dados para/do Azure Cosmos DB usando o Data Factory.| Microsoft Docs
description: Saiba como copiar dados de armazenamentos de dados de origem com suporte para o Azure Cosmos DB ou do Cosmos DB para armazenamentos de coletor com suporte, usando o Data Factory.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/28/2018
ms.author: jingwang
ms.openlocfilehash: 6c0921a466864bf2b07711cfcd1eac397c5ced83
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325346"
---
# <a name="copy-data-to-or-from-azure-cosmos-db-using-azure-data-factory"></a>Copiar dados de ou para o Azure Cosmos DB usando o Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-azure-documentdb-connector.md)
> * [Versão atual](connector-azure-cosmos-db.md)

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados do e para o Azure Cosmos DB (API do SQL). Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados do Azure Cosmos DB para qualquer armazenamento de dados de coletor com suporte ou copiar dados de qualquer armazenamento de dados de origem com suporte para o Azure Cosmos DB. Para obter uma lista de armazenamentos de dados com suporte, que funcionam como fontes/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector do Azure Cosmos DB dá suporte a:

- Cosmos DB [API do SQL](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction).
- Importação/exportação de documentos JSON no estado em que se encontram ou a cópia de dados de/para um conjunto de dados tabular, por exemplo, banco de dados SQL, arquivos CSV, etc.

Para copiar documentos no estado em que se encontram de/para arquivos JSON ou outra coleção do Cosmos DB, confira [Importação/Exportação de documentos JSON](#importexport-json-documents).

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas para o Azure Cosmos DB.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado do Azure Cosmos DB:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type deve ser definida como: **CosmosDb**. | SIM |
| connectionString |Especifique as informações necessárias para se conectar ao banco de dados do Azure Cosmos DB. Observe que você precisa especificar as informações de banco de dados na cadeia de conexão como no exemplo abaixo. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |SIM |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime do Azure ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não  |

**Exemplo:**

```json
{
    "name": "CosmosDbLinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre conjuntos de dados. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do Azure Cosmos DB.

Para copiar dados de/para o Azure Cosmos DB, defina a propriedade type do conjunto de dados como **DocumentDbCollection**. Há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type do conjunto de dados deve ser definida como: **DocumentDbCollection** |SIM |
| collectionName |Nome da coleção de documentos do Cosmos DB. |SIM |

**Exemplo:**

```json
{
    "name": "CosmosDbDataset",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Esquema do Data Factory

Para armazenamentos de dados sem esquema, como o Azure Cosmos DB, a atividade de cópia infere o esquema em uma das seguintes maneiras. Portanto, a menos que você queira [importar/exportar documentos JSON no estado em que se encontram](#importexport-json-documents), a melhor prática é especificar a estrutura dos dados na seção **estrutura**.

*. Se você especificar a estrutura dos dados usando a propriedade **structure** na definição de conjunto de dados, o serviço do Data Factory respeitará essa estrutura do esquema. Nesse caso, se uma linha não contiver um valor de uma coluna, um valor nulo será fornecido para ele.
*. Se você não especificar a estrutura dos dados usando a propriedade **structure** na definição de conjunto de dados, o serviço do Data Factory inferirá o esquema usando a primeira linha dos dados. Nesse caso, se a primeira linha não contiver o esquema completo, algumas colunas estarão ausentes no resultado da operação de cópia.

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte e pelo coletor do Azure Cosmos DB.

### <a name="azure-cosmos-db-as-source"></a>Azure Cosmos DB como fonte

Para copiar dados do Azure Cosmos DB, defina o tipo de fonte na atividade de cópia como **DocumentDbCollectionSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type da fonte da atividade de cópia deve ser definida como: **DocumentDbCollectionSource** |SIM |
| query |Especifique a consulta do Cosmos DB para ler dados.<br/><br/>Exemplo: `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Não  <br/><br/>Se não for especificada, a instrução SQL executada será: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Caractere especial para indicar que o documento está aninhado e como nivelar o conjunto de resultados.<br/><br/>Por exemplo, se uma consulta do Cosmos DB retornar um resultado aninhado `"Name": {"First": "John"}`, a atividade de cópia identificará o nome da coluna como "Name.First" com o valor "John" quando o nestedSeparator for um ponto. |Não (o padrão é o ponto `.`) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromCosmosDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Document DB input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DocumentDbCollectionSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-db-as-sink"></a>Azure Cosmos DB como coletor

Para copiar dados para o Azure Cosmos DB, defina o tipo de coletor na atividade de cópia como **DocumentDbCollectionSink**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | O tipo de propriedade do coletor da atividade de cópia deve ser definido como: **DocumentDbCollectionSink** |SIM |
| writeBehavior |Descrevem como gravar dados no Cosmos DB. Os valores permitidos são: `insert` e `upsert`.<br/>O comportamento de **upsert** é substituir o documento se já existir um documento com o mesmo id; caso contrário, insira-o. Nota O ADF gerará automaticamente um ID para o documento, se não estiver especificado no documento original ou no mapeamento de coluna, o que significa que você precisa garantir que o documento tenha um "id" para que o backup funcione conforme o esperado. |Não, o padrão é inserir |
| writeBatchSize | Data Factory use [o executor em massa do Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) para gravar dados no Cosmos DB. "writeBatchSize" Controla o tamanho dos documentos que fornecemos para a biblioteca de cada vez. Você pode tentar aumentar writeBatchSize para melhorar o desempenho. |Não  |
| nestingSeparator |Um caractere especial no nome da coluna de fonte para indicar que esse documento aninhado é necessário. <br/><br/>Por exemplo, `Name.First` na estrutura do conjunto de dados de saída gera a seguinte estrutura JSON no documento do Cosmos DB:`"Name": {"First": "[value maps to this column from source]"}` quando o nestedSeparator é um ponto. |Não (o padrão é o ponto `.`) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToCosmosDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DocumentDbCollectionSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="importexport-json-documents"></a>Importar/Exportar documentos JSON

Usando esse conector do Cosmos DB, você pode facilmente

* Importar documentos JSON de várias fontes para o Cosmos DB, incluindo Blob do Azure, Azure Data Lake Store e outros repositórios com base em arquivos com suporte pelo Azure Data Factory.
* Exportar documentos JSON da coleção do Cosmos DB para vários repositórios com base em arquivo.
* Copiar documentos entre duas coleções do Cosmos DB no estado em que se encontram.

Para conseguir realizar essa cópia independente de esquema:

* Ao usar a ferramenta de dados de cópia, marque a opção **"Exportar como estão para arquivos JSON ou uma coleção Cosmos DB"**.
* Ao usar a criação de atividade, não especifique a seção "estrutura" (ou esquema) nos conjuntos de dados do Cosmos DB nem a propriedade "nestingSeparator" na fonte/coletor do Cosmos DB na atividade de cópia. Ao importar de/exportar para arquivos JSON, no conjunto de dados correspondente do repositório de arquivos, especifique o tipo de formato como "JsonFormat" e a configuração "filePattern" corretamente (consulte a seção [Formato JSON](supported-file-formats-and-compression-codecs.md#json-format) para obter detalhes) e não especifique a seção "estrutura" (ou esquema) e ignore o restante das configurações de formato.

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
