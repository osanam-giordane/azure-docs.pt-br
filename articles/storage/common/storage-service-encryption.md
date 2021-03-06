---
title: Criptografia do Serviço de Armazenamento do Azure para dados em repouso | Microsoft Docs
description: Use o recurso Criptografia do Serviço de Armazenamento do Azure para criptografar o armazenamento de Blobs do Azure no lado do serviço ao armazenar os dados e descriptografá-lo ao recuperar os dados.
services: storage
author: lakasa
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 08/01/2018
ms.author: lakasa
ms.openlocfilehash: f35697139a4be49be8a645cfd4d451ad8e3c8094
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412348"
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Criptografia do Serviço de Armazenamento do Azure para dados em repouso
A criptografia de serviço de armazenamento do Azure para dados em repouso ajuda a proteger seus dados para atender aos compromissos de segurança e conformidade da organização. Com esse recurso, a plataforma de armazenamento do Azure criptografa automaticamente seus dados antes de persistir nos armazenamentos de Blobs do Azure, Azure Files ou Azure Queue e descriptografa os dados antes de recuperá-los. A manipulação de criptografia, criptografia em repouso, descriptografia e gerenciamento de chaves na Criptografia do Serviço de Armazenamento é transparente para os usuários. Todos os dados gravados na plataforma de armazenamento do Azure são criptografados por meio da [criptografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) de 256 bits, uma das mais fortes cifras de bloco disponíveis.

A Criptografia do Serviço de Armazenamento está habilitada para todas as contas de armazenamento novas e existentes e não pode ser desabilitada. Como os dados são protegidos por padrão, você não precisa modificar o código nem os aplicativos para aproveitar a Criptografia do Serviço de Armazenamento.

O recurso criptografa os dados automaticamente em:

- Armazenamento de Blobs do Azure, Arquivos do Azure, Armazenamento da Fila do Azure, Armazenamento da Tabela do Azure.  
- Ambos os níveis de desempenho (Standard e Premium).
- Ambos os modelos de implantação (Azure Resource Manager e clássico).

> [!Note]  
> A Criptografia do Serviço de Armazenamento não está disponível para [Discos Gerenciados do Azure](../../virtual-machines/windows/managed-disks-overview.md). Recomendamos que você use criptografia no nível do sistema operacional, como [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md), que usa o [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) padrão do setor no Windows e [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) no Linux para fornecer criptografia integrada com o KeyVault.

A Criptografia de Serviço de Armazenamento não afeta o desempenho dos serviços de armazenamento do Azure.

Use chaves de criptografia gerenciadas pela Microsoft com a Criptografia do Serviço de Armazenamento ou suas próprias chaves de criptografia. Para obter mais informações sobre como usar suas próprias chaves, consulte [Criptografia do Serviço de Armazenamento usando chaves gerenciadas pelo cliente no Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

## <a name="view-encryption-settings-in-the-azure-portal"></a>Exibir as configurações de criptografia no portal do Azure
Para exibir as configurações da Criptografia do Serviço de Armazenamento, entre no [portal do Azure](https://portal.azure.com) e selecione uma conta de armazenamento. No painel **CONFIGURAÇÕES**, selecione a configuração **Criptografia**.

![Captura de tela do portal mostrando a configuração de Criptografia](./media/storage-service-encryption/image1.png)

## <a name="faq-for-storage-service-encryption"></a>Perguntas frequentes sobre a Criptografia do Serviço de Armazenamento
**Como criptografo os dados em uma conta de armazenamento do Resource Manager?**  
A Criptografia de Serviço de Armazenamento está habilitada para todas as contas de armazenamento - clássicas e Gerenciador de Recursos, todos os arquivos existentes na conta de armazenamento criada antes da habilitação da criptografia serão criptografados retroativamente por um processo de criptografia em segundo plano.

**A criptografia de serviço de armazenamento está ativada por padrão quando eu crio uma conta de armazenamento?**  
Sim, a Criptografia do Serviço de Armazenamento está habilitada para todas as contas de armazenamento e para todos os serviços de armazenamento do Azure.

**Eu tenho uma conta de armazenamento do Resource Manager. Posso habilitar a Criptografia do Serviço de Armazenamento nela?**  
A Criptografia de Serviço de Armazenamento é habilitada por padrão em todas as contas de armazenamento existentes do Resource Manager. Isso é suportado para armazenamento de Blobs do Azure, arquivos do Azure, armazenamento de fila do Azure, armazenamento de tabela. 

**Posso desativar a criptografia na minha conta de armazenamento?**  
A criptografia é habilitada por padrão, e não há nenhuma provisão para desabilitar a criptografia para sua conta de armazenamento. 

**Quanto mais custa o armazenamento do Azure se a criptografia do serviço de armazenamento é habilitada?**  
Não há nenhum custo adicional.

**Posso usar minhas próprias chaves de criptografia?**  
Sim, você pode usar suas próprias chaves de criptografia. Para obter mais informações, consulte [Criptografia do Serviço de Armazenamento usando chaves gerenciadas pelo cliente no Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

**Posso revogar o acesso às chaves de criptografia?**  
Sim, se você [usar suas próprias chaves de criptografia](storage-service-encryption-customer-managed-keys.md) no Cofre de Chaves do Azure.

**A criptografia de serviço de armazenamento está disponível em discos gerenciados do Azure?**  
Não, a criptografia do serviço de armazenamento não está disponível para [Discos gerenciados do Azure](../../virtual-machines/windows/managed-disks-overview.md). Recomendamos que você use criptografia no nível do sistema operacional, como [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md), que usa o [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) padrão do setor no Windows e [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) no Linux para fornecer criptografia integrada com o KeyVault.

**Qual é a diferença entre a Criptografia de Serviço de Armazenamento e a Criptografia de Disco do Azure?**  
A Criptografia de Disco do Azure fornece integração entre soluções baseadas em SO, como BitLocker e DM-Crypt, e o Azure KeyVault. A Criptografia de Serviço de Armazenamento fornece criptografia nativamente na camada da plataforma de armazenamento do Azure, abaixo da máquina virtual.

**Eu tenho uma conta de armazenamento clássica. Posso habilitar a Criptografia do Serviço de Armazenamento nela?**  
A Criptografia de Serviço de Armazenamento está habilitada para todas as contas de armazenamento (clássica e Gerenciador de Recursos).

**Como posso criptografar dados em minha conta de armazenamento clássica?**  
Com a criptografia ativada por padrão, todos os dados armazenados nos serviços de armazenamento do Azure são criptografados automaticamente. 

**Posso criar contas de armazenamento com a criptografia de serviço de armazenamento ativada usando o Azure PowerShell e a CLI do Azure?**  
A Criptografia de Serviço de Armazenamento é ativada por padrão no momento da criação de qualquer conta de armazenamento (clássica ou Gerenciador de Recursos). Verifique as propriedades da conta usando o Azure PowerShell e a CLI do Azure.

**Minha conta de armazenamento está configurada para ser replicada de modo geo-redundante. Com a Criptografia do Serviço de Armazenamento, minha cópia redundante também será criptografada?**  
Sim, todas as cópias da conta de armazenamento são criptografadas. Há suporte para todas as opções de redundância – armazenamento com redundância local, armazenamento com redundância de zona, armazenamento com redundância geográfica e armazenamento com redundância geográfica com acesso de leitura.

**A criptografia de serviço de armazenamento é permitida apenas em regiões específicas?**  
Criptografia do serviço de armazenamento está disponível em todas as regiões.

**Armazenamento de serviço de criptografia FIPS 140-2 é compatível?**  
Sim, a criptografia do serviço de armazenamento é FIPS 140-2.

**Como posso contatar alguém se eu tiver problemas ou quiser fazer comentários?**  
Entre em contato com [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) para quaisquer problemas ou comentários relacionados à criptografia de serviço de armazenamento.

## <a name="next-steps"></a>Próximas etapas
O Armazenamento do Azure fornece um conjunto abrangente de funcionalidades de segurança que, juntas, ajudam os desenvolvedores a criar aplicativos seguros. Para obter mais informações, consulte o [Guia de segurança de armazenamento](../storage-security-guide.md).